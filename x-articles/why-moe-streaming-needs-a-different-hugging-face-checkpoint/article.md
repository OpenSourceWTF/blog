# Why MoE Streaming Needs a Different Kind of Hugging Face Checkpoint

**David Tai**, OpenSource.WTF

[INSERT IMAGE: moe-streaming-hero.png]
ALT: An SSD expert bank sends only selected weight tiles through a
unified-memory processor into a bounded resident pool
CAPTION: The router chooses the experts. The checkpoint determines whether
each one is already resident or has to come from SSD.

**48.04 tokens per second on Hy3, from a checkpoint that does not require the
user to rebuild its expert bank.**

That is the flagship result. It used the published oQ2e expert bank, all 79
routed layers resident as islands, a q4/gs64 resident trunk requantized at
startup, BF16 KV, and MTP depth 1 on an M5 Max with 128 GB of unified memory.
The q4 benchmark plan had a 96 GiB process ceiling. Across three
1,024-input/256-output runs, it averaged 48.04 tok/s. The matched AR control
averaged 41.36 tok/s.

The quality result matters just as much. On the complete 164-task
HumanEvalPlus suite, the q4 resident trunk passed 142 tasks. The q8 control
passed 143. McNemar's exact two-sided test was p=1.0. On this code evaluation,
startup requantization did not produce a measurable quality loss.

The 48.04 figure is the intended Hy3 performance result in this article. It is
a retained MTP runtime benchmark, not throughput from the current
AR-only `mtplx serve` route.

Getting that configuration onto another machine was the harder part. At
OpenSource.WTF, I had been trying to run mixture-of-experts models whose full
working set would not fit within the Mac's memory budget. My first plan was to
keep some weights on SSD and bring them into memory only when the model needed
them.

That requires two fast lookups: which weights does the token need next, and
where are those weights on disk? The MoE router answers the first. The
checkpoint has to answer the second. Most checkpoints are laid out for loading
the whole model, not for fetching one expert while generation is running.

## Why MoE models can leave most experts on SSD

A dense transformer uses nearly every feed-forward weight for every token. If
those weights live on SSD, storage latency follows the model through every
layer. There is little work to skip.

An MoE model replaces some of those dense feed-forward blocks with a collection
of expert networks and a router. The router scores the experts, selects a small
subset, and combines their outputs. Experts that were not selected do no work
for that token.

Tencent describes Hy3 as a
[295-billion-parameter model with 21 billion active parameters](https://huggingface.co/tencent/Hy3#model-introduction).
It has 192 routed experts and selects eight in each sparse layer. A token uses
only a fraction of the expert weights stored in the checkpoint.

Routing is still sequential. Layer 32 cannot select its experts until layer 31
has produced its output:

```text
attention -> router -> selected expert IDs -> expert MLPs -> combine -> next layer
```

The router remains authoritative. Streaming only changes where the selected
expert weights come from. A selected expert may already be resident, or the
runtime may have to read it from SSD into a fixed memory slot before its MLP
can run.

The current Hy3 package is about 97.6 GB, including an 80,518,053,888-byte
expert bank. Streaming does not make that download smaller. It avoids keeping
the bank, the resident model, the KV cache, and the rest of the process in
unified memory at the same time.

## How a checkpoint lays tensors out on disk

A Hugging Face checkpoint serializes named tensors into byte ranges. Safetensors
stores each tensor's dtype, shape, and offsets in an inspectable header, followed
by the tensor data. Large checkpoints split those tensors across shards and use
an index to map names to files.

But a container can describe every tensor correctly and still arrange the
bytes poorly for streaming. A normal load is asking:

> Which files contain all the tensors needed to construct this model?

An SSD-streamed MoE layer is asking:

> The router just selected expert 77 in layer 31. Where is its complete record,
> and can I read it directly into the slot Metal will use?

In the source checkpoints we began with, one expert's gate, up, and down
projections—along with their quantization scales and biases—lived in separate
component-major regions. A single logical expert was assembled from slices of
several tensors, sometimes spread across checkpoint shards.

A bulk loader can assemble those regions once while constructing the model. A
streaming runtime would repeat several positional reads and assembly work every
time the router selected that expert.

## Our first Hy3 setup kept two copies of every routed expert

The first SSD-streamed version of Hy3 worked. It was also a terrible thing to
ask another person to install.

You first downloaded the ordinary checkpoint. Then you ran a local conversion
that extracted and rearranged all of its routed experts into a second copy the
runtime could stream. The conversion was long, and the machine needed enough
temporary storage for both layouts. The result used less memory during
inference while asking for roughly twice the expert storage before the first
prompt.

It proved that paging worked, but not that the model was distributable. To fix
that, `(layer, expert)` had to resolve to one bounded record in the layout the
kernel expects, without loading unrelated experts or rebuilding the record in
temporary buffers.

We now publish the models in that form:

- [`OpensourceWTF/Hy3-oQ2e-MTPLX-streaming`](https://huggingface.co/OpensourceWTF/Hy3-oQ2e-MTPLX-streaming)
- [`OpensourceWTF/GLM-5.2-t158-MTPLX-streaming`](https://huggingface.co/OpensourceWTF/GLM-5.2-t158-MTPLX-streaming)

They pair with [`OpenSourceWTF/mtplx-moe`](https://github.com/OpenSourceWTF/mtplx-moe),
our fork of [Youssof Altoukhi's MTPLX](https://github.com/youssofal/MTPLX).

## Packing each expert into one record

[INSERT IMAGE: checkpoint-to-expert-bank.png]
ALT: Ordinary component-major safetensors are extracted into resident shards,
an authoritative manifest, and fixed expert-major records in experts.bin
CAPTION: The published checkpoint contains the expert-major bank MTPLX
consumes, so each user does not have to build a second local copy.

Our packing step turns the checkpoint inside out:

1. Tensors used throughout inference—attention, routers, embeddings, norms,
   shared weights, and the output path—remain in resident safetensors shards.
2. Every routed expert becomes one aligned, expert-major record in a serialized
   bank.
3. An authoritative manifest records the model key, source provenance, tensor
   shapes, record offsets and lengths, storage layout, file sizes, and digests.
4. The complete repack is verified before publication.

`experts.bin` is a sequence of fixed records. The manifest defines their order,
geometry, and meaning. MTPLX can resolve a `(layer, expert)` pair to one offset
and length without searching tensor names or assembling slices.

The Hugging Face repository contains the resident tensors and expert bank, not
a second copy of the original expert safetensors. `mtplx pull` or
`mtplx serve --download` retrieves the layout the runtime consumes. No source
checkpoint or local repack is required.

## The manifest tells MTPLX what each record contains

If a streamed record is wrong, the model might not fail until the router
selects it. MTPLX therefore checks the model key, record geometry, codec,
immutable Hugging Face revision, bank length, and SHA-256 digest before
constructing the model. Any mismatch stops startup.

A successful admission writes a revision- and digest-bound receipt outside the
immutable Hugging Face snapshot. Later launches can reuse that receipt instead
of hashing the 80.5 GB bank again.

Once the artifact, shapes, memory plan, and kernel route have passed that
construction boundary, the production path executes them directly. It does not
re-prove invariant model metadata on every token.

## From a router decision to a safe memory slot

The model keeps its router and common trunk resident. At each routed layer, the
router produces expert IDs and weights in the model's original order. The
runtime resolves each ID through one of two routes installed at construction.

An **island** holds every routed expert for one transformer layer in unified
memory. That layer never needs an expert-bank read during generation.

A **paged layer** has a fixed bank of component slots. A hit uses the current
resident slot. A miss issues a checked positional read for the selected record
into an available persistent or transient slot. The slot has a generation and
a pin: storage cannot overwrite it until the final Metal command using that
generation has completed.

[INSERT IMAGE: islands-and-paging.png]
ALT: A resident router sends selected expert IDs either to a fully resident
island or through hits and checked misses in a fixed paged slot bank
CAPTION: Islands keep a whole routed layer resident. Paged layers use bounded
slots and checked SSD reads, then restore the router's original assignment
order.

Persistent and transient slot counts are fixed before the model is installed.
A cache miss may replace an eligible slot, but it cannot grow the cache or
create a second copy of the expert corpus.

After the selected experts execute, their outputs are restored to the router's
original assignment order and combined with the original routing weights.
Whether an expert was an island hit, a slot hit, or an SSD miss must not change
the arithmetic.

On Apple Silicon, CPU and GPU work share unified memory. The native reader can
fill stable MLX buffers that Metal will consume. A miss still pays SSD latency,
but it does not add a separate CPU-to-VRAM copy.

## Hy3 profiles decide what stays in memory

Hy3 ships three promoted memory profiles:

| Profile | Process ceiling | Expert placement | Resident trunk |
|---|---:|---|---|
| `hy3-oq2e-64` | 71 GiB | 0 islands; 49.992 GiB frequency cache | q4 |
| `hy3-oq2e-88` | 95 GiB | 74 islands; 5 streamed layers; 2 GiB cache | q8 |
| `hy3-oq2e-96` | 103 GiB | all 79 routed layers in islands | q8 |

The number in the profile name is the weight envelope, not the amount of RAM
the machine contains. Each process ceiling includes a 7 GiB runtime reserve,
and all three promoted profiles cap aggregate live KV at 4,096 tokens.

With `--expert-profile auto`, the fork chooses the largest promoted profile
that fits both installed memory and memory available at launch. If you select a
profile by name, startup runs the same preflight and fails clearly if it does
not fit. It does not quietly downgrade.

All three profiles use the same checkpoint. The 64 GiB envelope pages every
routed layer. The 88 GiB envelope pages five. The 96 GiB envelope keeps all 79
in islands and does not read the bank during steady decode.

These are capacity plans, not competing headline benchmarks. The flagship uses
the all-island geometry, then requantizes the resident trunk to q4 and runs MTP
at depth 1. The appendix retains the separate profile measurements without
mixing their protocols into the main result.

## Building the 48 tok/s Hy3 configuration

The published checkpoint keeps its resident trunk in q8. To test q4 without
publishing another checkpoint or touching the 80.5 GB expert bank, we used a
small configuration overlay:

```json
{
  "proj_requant": "q4"
}
```

At model construction, that setting converts supported resident q8 `*_proj`
matrices to q4 with group size 64. It does not change `experts.bin`, router
gates, embeddings, the language-model head, norms, or biases.

We called this dynamic requantization because it is applied to a downloaded
checkpoint at startup rather than baked into another release. It is not
dynamic during generation. Once construction finishes, the installed route
uses the q4 arrays directly; there is no per-token precision check or q8
fallback in the measured path.

We also ran the q4 overlay with two smaller memory plans. To keep that
comparison controlled, all three plans used the same 320-token code prompt,
generated 256 tokens, and retained three repetitions in both AR and MTP depth
1:

| q4 benchmark plan | Process ceiling | Expert placement | AR decode | MTP D1 decode |
|---|---:|---|---:|---:|
| 64 GiB envelope | 71 GiB | no islands; 49.992 GiB cache | **9.17 tok/s** | **10.78 tok/s** |
| 80 GiB envelope | 87 GiB | 69 islands; 10 paged layers | **21.16 tok/s** | **22.32 tok/s** |
| `88e`, full resident | 96 GiB | all 79 layers in islands | **43.31 tok/s** | **47.65 tok/s** |

These are the requant benchmark plans, not the q8 promoted profile names in
the preceding section. The controlled sweep shows the cost of paging as the
memory budget shrinks. Its full-resident mean is 47.65 tok/s because it used
the shared 320-token protocol. The longer 1,024-token arm of record remains the
48.04 tok/s result from the opening.

The appendix includes every repetition from both q4 benchmarks and the
complete HumanEvalPlus receipts.

## GLM-5.2 needs a different expert-bank format

The GLM package is about 187 GiB on disk. Its
`experts-q1-t158.bin` alone is 169,869,312,000 bytes and stores ternary expert
weights with group scales. Hy3's affine dense-island kernels cannot interpret
that representation, so the GLM route does not support Hy3's island settings.
Its measured configuration keeps 75 layers on bounded paging instead.

Contiguous records are not a universal expert format. The codec and record
geometry still have to match the execution kernel.

In the retained 96 GiB lane, a 72 GiB expert cache held 116 slots per streamed
layer. Two 1,024-input/1,024-output runs averaged 5.33 tok/s in AR and
5.93 tok/s at MTP depth 3. The MTP lane used a separate Q4 layer-78 draft
head. It is benchmark evidence, not a mode exposed by the current AR-only
server.

The t158 conversion is also lossy. It has construction-time numeric checks, but
we do not yet have a task-quality receipt for it: no HumanEval, MBPP, or
perplexity result. We are publishing it as a runtime engineering artifact, not
recommending it as a quality-preserving GLM release.

## Downloading Hy3 without the conversion step

[`OpenSourceWTF/mtplx-moe`](https://github.com/OpenSourceWTF/mtplx-moe) is our
fork of MTPLX. It is not on PyPI and retains upstream's `mtplx` distribution,
import, and CLI names, so install it in a dedicated environment:

```bash
MTPLX_MOE_VENV="$HOME/.venvs/mtplx-moe"
python3 -m venv "$MTPLX_MOE_VENV"
"$MTPLX_MOE_VENV/bin/python" -m pip install --upgrade pip
"$MTPLX_MOE_VENV/bin/python" -m pip install \
  "mtplx @ git+https://github.com/OpenSourceWTF/mtplx-moe.git@main"
"$MTPLX_MOE_VENV/bin/python" -m pip check
"$MTPLX_MOE_VENV/bin/mtplx" --version
```

The version must contain `+opensourcewtf.moe`. If it does not, stop there: you
are not running this fork. Use the venv's absolute `mtplx` path as shown,
especially if upstream MTPLX, its Homebrew install, or its Mac app is also on
the machine.

Hy3 can download and launch in one command:

```bash
"$MTPLX_MOE_VENV/bin/mtplx" serve \
  --model OpensourceWTF/Hy3-oQ2e-MTPLX-streaming \
  --download
```

That selects the largest promoted profile that passes preflight. After startup,
check which route was constructed:

```bash
curl -fsS http://127.0.0.1:8000/health | python3 -m json.tool
curl -fsS http://127.0.0.1:8000/v1/models | python3 -m json.tool
```

The current streamed serving route is AR-only. The exact commands for named
Hy3 profiles, q4 resident-trunk overlays, the measured GLM configuration, and
OpenAI-compatible clients live in the fork's
[SSD-streamed MoE guide](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/docs/advanced/ssd-streamed-moe.md).

## The storage format is part of the runtime

The router tells MTPLX which expert comes next. The checkpoint turns that
decision into one bounded read. The manifest proves that the bytes belong to
the model, the memory plan provides a fixed destination, and slot ownership
keeps the record alive until Metal is finished.

The 48.04 tok/s result does not come from making SSD misses free. The flagship
keeps all 79 routed layers resident. The streaming checkpoint matters because
the same published artifact can also run under smaller memory plans without a
local conversion or a second copy of the expert weights.

`experts.bin` is therefore the storage layout of the routed model, not a cache
each user derives after downloading it. The first Hy3 version asked every user
to rebuild that layout. Publishing it directly is what made “download the model
and run it” an honest instruction.

## Appendix: the receipts behind the numbers

These are the retained results cited in this article. The linked JSON files
contain the complete receipts rather than only the rounded claims below.

### Hy3: AR and MTP

| Profile | Repetitions | AR decode | MTP D1 | MTP D2 | MTP D3 | Receipt |
|---|---:|---:|---:|---:|---:|---|
| `hy3-oq2e-64` | 3 | **9.31** | **11.59** | **11.15** | 9.63† | promotion receipt `14c8b57` |
| `hy3-oq2e-88` | 1 | **22.35** | — | 25.93† | **22.15** | [JSON](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/evals/tier2/oq2e_88.json) |
| `hy3-oq2e-96` | 1 | **30.17** | — | 36.82† | **31.81** | [JSON](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/evals/tier2/oq2e_champion_96.json) |

All figures are decode tok/s. The 64 GiB values are three-run means; its AR
repetitions were 9.24, 9.39, and 9.30 tok/s. D1 and D2 retained token parity.
D3 diverged from the AR reference at token 805. In the single-run 88 and
96 GiB receipts, D2 had one divergence from the corresponding AR reference;
D3 retained token parity.

The installed 64 GiB profile uses the same zero-island, cache-heavy geometry
but caps live KV at 4,096 tokens and has a 71 GiB process ceiling. The retained
64 GiB performance receipt used a 16K-KV ceiling and a 74.75 GiB process
ceiling. The 88 and 96 GiB results used the installed 4K ceiling. These numbers
should not be used to calculate a controlled percentage gain between profiles.

### Hy3: startup q4 requantization

The full-resident arm of record used q4/gs64 resident projections, the
unchanged oQ2e expert bank, all 79 islands, BF16 KV, a 1,024-token real-code
prompt, and 256 generated tokens on an M5 Max with 128 GB of unified memory.

| Repetition | AR decode | MTP depth 1 decode | Accepted drafts per verify | Token parity |
|---:|---:|---:|---:|---|
| 1 | 41.37 tok/s | 47.45 tok/s | 0.8984 | yes |
| 2 | 41.35 tok/s | 48.64 tok/s | 0.8984 | yes |
| 3 | 41.37 tok/s | 48.04 tok/s | 0.8984 | yes |
| **Mean** | **41.36 tok/s** | **48.04 tok/s** | **0.8984** | **yes** |

The [compact JSON receipt](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/evals/tier2/hy3_oq2e_rq4_flagship_summary.json)
records the exact values, hardware, configuration, source commit, and hashes
of the three source receipts.

This table compares AR and MTP on the same q4 route; it is not a q4-versus-q8
speed comparison.

The second q4 benchmark used one 320-token code prompt across three memory
plans. Each cell below is a complete 256-token generation. All MTP D1 runs
retained token parity with their paired AR reference.

| q4 plan | AR repetitions | AR mean | MTP D1 repetitions | D1 mean |
|---|---|---:|---|---:|
| 64 GiB envelope | 9.98, 8.70, 8.83 | **9.17 tok/s** | 10.63, 10.56, 11.16 | **10.78 tok/s** |
| 80 GiB envelope | 21.22, 21.13, 21.13 | **21.16 tok/s** | 22.47, 22.27, 22.23 | **22.32 tok/s** |
| `88e`, full resident | 43.28, 43.35, 43.30 | **43.31 tok/s** | 49.24, 48.47, 45.23 | **47.65 tok/s** |

The
[attached compact JSON](https://opensource.wtf/content/blog/images/hy3-rq4-memory-plan-sweep.json)
contains every repetition, the exact means, the shared protocol, source paths,
source commit, and SHA-256 hashes of all nine raw receipts.

The quality comparison used HumanEvalPlus:

| Resident trunk | Coverage | Passed | pass@1 | Complete receipt |
|---|---:|---:|---:|---|
| q8, initial smoke | 20 tasks | 19 | 95.0% | [JSON](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/evals/tier2/humaneval_oq2e.json) |
| q4, initial smoke | 20 tasks | 19 | 95.0% | [JSON](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/evals/tier2/humaneval_oq2e_rq4.json) |
| q8, full suite | 164 tasks | 143 | 87.2% | [JSON](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/evals/tier2/humaneval_oq2e_full164_q8.json) |
| q4, full suite | 164 tasks | 142 | 86.6% | [JSON](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/evals/tier2/humaneval_oq2e_full164_rq4.json) |

The 20-task runs were smoke tests and are superseded by the full suite. The
164-task runs used HumanEvalPlus v0.1.10, one sample per task, greedy decoding,
temperature 0, seed 42, and the chat endpoint. Both completed without request
errors. The paired exact McNemar result for q4 against q8 was p=1.0.

### GLM-5.2: AR and MTP

| Expert cache | Repetitions | AR decode | AR mean | MTP D3 decode | D3 mean |
|---|---:|---|---:|---|---:|
| 54 GiB control | 2 | 4.50, 4.46 tok/s | **4.48 tok/s** | 4.93, 5.04 tok/s | **4.99 tok/s** |
| 72 GiB measured plan | 2 | 5.39, 5.26 tok/s | **5.33 tok/s** | 5.91, 5.96 tok/s | **5.93 tok/s** |

Both arms used the t158 expert bank, 75 streamed layers, a 96 GiB process
ceiling, a 12 GiB reserve, 48 transient slots, a Q4 layer-78 MTP head, a
1,024-token input, and 1,024 generated tokens. The second pair reversed arm
order. All benchmark gates passed, and the 72 GiB AR and D3 rows produced the
same token hash.

The [complete GLM JSON receipt](https://github.com/OpenSourceWTF/mtplx-moe/blob/main/research/glm52-q1t-cache72-benchmark-20260718.json)
contains the per-run throughput, cache hit rates, miss counts, peak memory, and
quality boundary. Its token parity is internal to the t158 artifact. Because
t158 is lossy relative to the source Q2 weights and has no task evaluation,
these figures are throughput claims, not evidence of preserved GLM quality.

---

The implementation, installation notes, and retained receipts are in
[`OpenSourceWTF/mtplx-moe`](https://github.com/OpenSourceWTF/mtplx-moe). The
fork builds on [MTPLX](https://github.com/youssofal/MTPLX) and
[MLX](https://github.com/ml-explore/mlx).
