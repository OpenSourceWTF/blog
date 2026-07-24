# Make your MLX kernels faster with the MLX Profiler

**David Tai**, OpenSource.WTF

[INSERT IMAGE: a3b-profiler-performance-ladder-no-section-number.png]
ALT: A profiler comparison showing fewer CPU dispatches after the A3B optimization
CAPTION: The faster A3B path did slightly more GPU work. It won because the CPU had less work to send.

I built [mlx-profiler](https://github.com/OpenSourceWTF/mlx-profiler) after a
few “faster” kernels made Qwen3.6-35B-A3B slower.

The kernels really were faster on the GPU. But they also added launches,
temporary tensors, and CPU work. The full model lost speed.

That is the main idea behind the profiler:

> **Do not optimize the kernel in isolation. Optimize the whole cycle.**

Here is the short version of how to use it.

## 1. Capture your workload

Build the instrumented MLX fork by following its
[quickstart](https://github.com/OpenSourceWTF/mlx-profiler/blob/main/PROFILER.md#first-census-quickstart).
Then run your normal Python workload with one environment variable:

```sh
MLX_DISPATCH_CENSUS="$PWD/baseline.jsonl" \
  python your_workload.py
```

Set the variable before Python imports MLX, and let the process exit normally.

The profiler records:

- every GPU dispatch and its kernel name;
- CPU time spent building Metal command buffers;
- GPU start and end times;
- allocator, scheduler, and synchronization waits; and
- whether the capture completed without dropping data.

One important detail: it does not add a GPU wait just to collect timing.

## 2. Open the visualizer

Run the [Metal Dispatch Workbench](https://github.com/OpenSourceWTF/metal-dispatch-viz)
locally:

```sh
git clone https://github.com/OpenSourceWTF/metal-dispatch-viz.git
cd metal-dispatch-viz
npm ci
npm start -- --trace-dir /absolute/path/to/your/traces
```

Open `http://127.0.0.1:4173/` and choose your trace.

Your files stay local. The server is read-only and does not upload the trace.
If you just want to explore first, the
[public workbench](https://mlx-profiler.opensource.wtf) has real example runs.

## 3. Look for the expensive shape

[INSERT IMAGE: mlx-profiler-before-v230-k1-cycle-aligned.png]
ALT: MLX Profiler timeline before the A3B optimization, with a dense stream of CPU dispatches
CAPTION: Before: 1,976 dispatches in one active A3B decode burst.

The timeline puts CPU encoding, GPU execution, command buffers, and waits on
the same clock.

A few useful things to look for:

- **Lots of tiny dispatches.** One fused kernel may remove several launches.
- **Gaps before GPU work.** The GPU may be waiting for the CPU to feed it.
- **Exposed host time.** CPU work hidden behind the GPU is less urgent. CPU
  work outside GPU execution can extend the cycle.
- **Repeated kernel families.** The sortable kernel table makes noisy patterns
  easy to spot.
- **Waits.** The wait view separates allocator, scheduler, backpressure, and
  explicit synchronization stalls.

Drag over one representative cycle and switch to **Analyze**. The workbench
will recalculate the numbers for that exact range instead of the full launch.

There is also an **Export for AI** button. It creates a local Markdown or JSON
snapshot of the visible timeline, including evidence limits, without sending
anything to a model.

## 4. Make one change and compare again

Use the trace to form a small, testable idea:

- fuse a chain of tiny operations;
- remove an intermediate tensor;
- give one threadgroup ownership of a complete small row;
- reuse state you already calculated; or
- move a CPU/GPU round trip onto the device.

Check correctness, benchmark the full workload without the profiler, then
capture the new version under the same model, prompt, seed, and settings.

[INSERT IMAGE: mlx-profiler-after-pr174-k1-cycle-aligned.png]
ALT: MLX Profiler timeline after the A3B optimization, with fewer CPU dispatches and less exposed host time
CAPTION: After: 1,459 dispatches and much less exposed CPU encoding.

In the A3B run, the optimized path removed 517 dispatches:

| One active decode burst | Before | After |
|---|---:|---:|
| Dispatches | 1,976 | 1,459 |
| Host encode | 6.895 ms | 5.620 ms |
| Exposed host encode | 635.91 µs | 205.58 µs |
| GPU work | 7.70 ms | 7.78 ms |

Yes, GPU work went up slightly. The cycle still got faster because the CPU
created a much shorter command stream.

That is the cool part of seeing both sides together. You stop guessing from one
kernel timer and start seeing what the machine is actually waiting for.

Capture. Look for gaps and launch noise. Make one change. Compare the same
cycle.

Keep the changes that make the real workload faster.
