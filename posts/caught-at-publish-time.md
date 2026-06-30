---
title: "Catching Malware at Publish Time"
date: "2026-06-29"
excerpt: "About 7,500 new packages ship to npm and PyPI every day, but who is validating them?"
tags: ["announcement", "leaderboard", "supply-chain", "security", "scanning", "malware", "open-source"]
image: "/content/blog/images/scan-dashboard.png"
---

# Catching Malware at Publish Time

*About 7,500 new packages ship to npm and PyPI every day, plus tens of thousands more releases of existing ones. We read the load-bearing ones with an LLM, run them in a sandbox, and write the verdict to a blockchain.*

[Last time](https://opensource.wtf/blog/launch-the-load-bearing-internet) we ended on a problem. We spend a fortune guarding production, the servers out in the world, and little guarding the machine where the software actually gets built. The supply chain runs right through that machine. Every `npm install` pulls code that you implicitly trust.

The leaderboard tells you which packages the world leans on. It can't tell you if one of them had gone bad. We now added the part that can. It reads new releases, runs them, and flags the ones acting like malware.  Its fast enough to catch a compromised version while its being distributed. The verdicts go on a public blockchain, so they stick around accessible to anyone iwthout being dependent on hosting factors.

## Drinking From a Firehose

Open the **Live Feed** and watch it for a minute.

![The Live Feed: a stream of npm and PyPI packages publishing in real time, each row stamped with how long ago it landed, the newest seven and nineteen seconds old.](images/feed-stream.png)

<small>The live publish stream. The numbers on the right are how long ago each version shipped.</small>

Packages land constantly. On a normal weekday npm and PyPI register about 7,500 brand-new packages, plus tens of thousands of releases and updates to ones that already exist, on the order of 85,000 events a day. Nobody reviews 85,000 a day, and for most of them nobody reviews the code at all. A maintainer publishes a version, it's live in seconds, and the first person to install it is the one who finds out if something is wrong.

This isn't a new problem, and we aren't the only ones on it. [SafeDep](https://safedep.io/how-safedep-works) does it too. But few people are doing it for the open-source community, so we are. We haven't caught up to the whole feed yet, so we start with the packages the leaderboard already ranks, the top 10,000 in each ecosystem, and take new releases off the feed as they publish. 

We look at each package three ways. First we run [OSV Scanner](https://github.com/google/osv-scanner), the standard open-source pass against the public advisory databases. Then an LLM, Qwen3.6 27B, reads the source and works out what the code is doing with our custom per ecosystem prompt. Then a sandbox installs the package and watches what it reaches for, like the keys and tokens we leave out as bait, and a second LLM run analyzes logs from the sandbox to look at egress, or honey pot access. From those three we mark the version High-risk, Deceptive, Insecure, Inconclusive, or Clean.

## Enhancing OSV Scanner Pattern Matching with Semantic Scans for Fuzzy Matching

OSV Scanner is the a first pass, and it's a good one. It checks a package against the public advisory databases, the list of problems someone has already found and written up. It's fast, and it's right about everything on that list. What it can't do is catch something no one has reported yet.

That's where the AI comes in. It reads the source and tries to reason on what the code actually does, not just whether it matches a known signature. It can catch things that aren't on any list yet, like an install script reaching for keys, a dependency list built to get pulled in by accident, or code obfuscated to hide what it's doing. OSV handles the known patterns, and the AI handles suspicious new patterns.

Take `aid-guard1`. It's the #507 most-depended-upon package on npm, listed as a dependency by 7,514 others, and it gets downloaded 140 times a week. Those two numbers don't belong together, and that gap is the first thing wrong with it. The AI read it and flagged it high-risk. We'll go through its full page later on.

## Catching Supply Chain Attacks In Real Time

Reading a package is half the job. Catching it fast is the other half. The scanner works off that same publish feed, so it usually reaches a release within an hour of publish, not a day later in a security writeup after the case.

![The Scans dashboard: 16,214 package versions scanned, 120 flagged, a live queue, and two packages being scanned at this moment.](images/scan-dashboard.png)

<small>The Scans tab. The bar is every version checked so far, sorted by verdict; the left column is the live queue.</small>

We flag any verdict on a version as soon as possible. The dangerous moment for a poisoned release is right after it ships, before anyone notices. While the recommendation is to wait 2-3 hours before updating to the latest releases as a work around, someone still needs to detect issues.  The npm worms this past year each hit hundreds of packages in a day. On the board that looks like a sudden batch of high-risk versions, all brand-new, going up while the worm still spreads. That's when a warning helps.

## The Verdicts Are Hosted On An Immutable Blockchain

A verdict isn't auditable if someone can quietly change it later. So we write every finished verdict to the **TEA blockchain**.  Scans aren't replaced, they can't be due to the rules of the claims contract, we can only publish new scans so the community has a truthful history of our determinations based and quality of our algorithm.  The permanence and immutability are the reasons why we use a chain at all. Click any verdict and pull up the transaction yourself.

![The per-version view for a flagged package, showing an on-chain record of the high-risk verdict with a signed payload and a link to the transaction.](images/aidguard-onchain.png)

<small>Each verdict is attested on-chain: signed, dated, and linked to a transaction anyone can pull up, sitting apart from our own database.</small>

## Features

Here's `aid-guard1`'s full page, every check the scanner ran in one place.

![The aid-guard1 detail page, marked High-risk for dependency-confusion and typosquatting, with the AI's report and a critical finding on the dependency list.](images/scan-highrisk.png)

<small>`aid-guard1`: 7,514 dependents, 140 downloads a week, and a dependency list built to be installed by accident.</small>

Top to bottom:

- **Verdict.** High-risk, with the attack type beside it: dependency-confusion and typosquatting.
- **Two checks, side by side.** The AI's read of the source and the sandbox run, each with its own result.
- **Plain-English writeup.** What the package does and why it's dangerous, in words anyone can follow.
- **The findings.** Each one pinned to its line in the manifest, with the junk dependency names quoted from the source.
- **Leaderboard scores.** Rank, dependents, and influence, so you can see how much the package was holding up.
- **OSV cross-check.** What the public advisory databases say, next to our own call.
- **Version history.** Every release scored on its own, each with its on-chain receipt.

A maintainer reads it and sees what tripped the package. Someone about to install it sees the verdict and stops.

## What's Next

Today the scanner covers npm and PyPI. Next we're extending it to [pkgx](https://pkgx.sh) and its [pantry](https://github.com/pkgxdev/pantry), the open catalog of packages pkgx installs and runs. It's a different slice of the ecosystem, the command-line tools developers run directly, with the same exposure: a package ships, someone installs it, and nobody read the code in between. The same three checks carry over, so we'll run OSV, the AI read, and the sandbox there too, and flag malicious pantry packages the way we flag them on npm.

We haven't published the scanner's source yet. We want more testing on it first, then we'll open it up.

If you maintain pantry packages, or want to help us reach more ecosystems, come say hi: the [TEA Discord](https://discord.gg/ZfRnVBWJb) and [@OpensourceWTF](https://x.com/OpensourceWTF) on X.

---

## Check The Opensource.WTF Leaderboard Registry Yourself

*For developers. Each verdict is an [EAS](https://attest.org) attestation on the TEA chain. Here is what one holds and how to take it apart.*

The contracts and the signer, all public:

| | |
|---|---|
| Chain | TEA, chainId `6122` |
| RPC | `https://rpc.tea.xyz` |
| Explorer | `https://explorer.tea.xyz` |
| EAS | `0x4a2Dd346f351c3FE890Ff4B47E8124f11fa84a82` |
| Schema UID | `0x5865e88ad6ef941ccb43ae7f7225dbb419b2797a3a946ddd6a193c5492ce5161` |
| Resolver | `0xcb78Fc94aa2fb160d3d89702b0d6078E513e1Bb8` |
| Attester | `0xa25C7910208cB3690E9d1e1F44ff45927A0bBc85` |

Every attestation's `data` is ABI-encoded against one schema:

```
bytes32 subjectKey,
string  environment,
string  package,
string  version,
uint32  runNumber,
string  skill,
uint8   status,
uint8   classification,
bytes32 reportHash
```

| field | type | meaning |
|---|---|---|
| `subjectKey` | bytes32 | `keccak256(abi.encode(environment, package, version))`, the per-version key the resolver dedupes on |
| `environment` | string | ecosystem: `npm` or `pypi` |
| `package` | string | package name |
| `version` | string | the exact version scanned |
| `runNumber` | uint32 | the scan's index for this version: `0` for the first scan, `1` for the first re-scan, up by one after that. The resolver keeps `(attester, subjectKey, runNumber)` unique |
| `skill` | string | which scanner wrote it, e.g. `supply-chain` |
| `status` | uint8 | `0` unknown, `1` completed, `2` errored |
| `classification` | uint8 | `0` unknown, `1` clean, `2` insecure, `3` deceptive, `4` high-risk |
| `reportHash` | bytes32 | `keccak256` of the full findings JSON the site serves for that scan (zero if none) |

The word on the package page is `classification`. The `reportHash` ties the verdict to its detail: refetch the report the API serves and check that `keccak256(utf8(json))` still matches, and you know the findings haven't been swapped under it.

Decode one with [Foundry](https://book.getfoundry.sh)'s `cast`. Start from a UID, which is on the package page next to the verdict:

```bash
EAS=0x4a2Dd346f351c3FE890Ff4B47E8124f11fa84a82
RPC=https://rpc.tea.xyz
UID=0x0106bf5515847ae8518a1b75a2ea54ded5fe567948e2147dcfa2417853cf8a0a   # aid-guard1@2.7.35

# read the attestation; its last field is the ABI-encoded ScanRecord
DATA=$(cast call "$EAS" \
  "getAttestation(bytes32)((bytes32,bytes32,uint64,uint64,uint64,bytes32,address,address,bool,bytes))" \
  "$UID" --rpc-url "$RPC" | tail -1)

# decode that into the schema fields
cast abi-decode \
  "scan()(bytes32,string,string,string,uint32,string,uint8,uint8,bytes32)" "$DATA"
```

which prints the record (this one is real, on-chain now):

```
0x3da85d70…143ccab   subjectKey
npm                  environment
aid-guard1           package
2.7.35               version
0                    runNumber
supply-chain         skill
1                    status          → completed
4                    classification  → high-risk
0xb44398a1…105e2e0   reportHash
```

No UID on hand? Derive it from the package coordinates and a run number through the resolver.

The **run number** is the scan's index for that exact version. The first scan of a version is run `0`, the first re-scan is `1`, and it climbs by one each scan after that, in order. The resolver keys every verdict on `(attester, subjectKey, runNumber)`, so you have to say which run you want. Most versions only ever have run `0`. Because the runs are contiguous from `0`, you can find the latest by counting up until `uidFor` returns `0x0` and taking the last UID before it.

```bash
RESOLVER=0xcb78Fc94aa2fb160d3d89702b0d6078E513e1Bb8
ATTESTER=0xa25C7910208cB3690E9d1e1F44ff45927A0bBc85

# subjectKey = keccak256(abi.encode(environment, package, version))
SUBJECT=$(cast keccak "$(cast abi-encode 'f(string,string,string)' npm aid-guard1 2.7.35)")
# → 0x3da85d70…143ccab

# run 0 — the first (and, for aid-guard1, only) scan of this version
cast call "$RESOLVER" "uidFor(address,bytes32,uint32)(bytes32)" \
  "$ATTESTER" "$SUBJECT" 0 --rpc-url "$RPC"
# → 0x0106bf55…cf8a0a

# re-scanned version? walk runs up from 0; the last non-zero uid is the latest:
for RUN in 0 1 2 3; do
  cast call "$RESOLVER" "uidFor(address,bytes32,uint32)(bytes32)" \
    "$ATTESTER" "$SUBJECT" "$RUN" --rpc-url "$RPC"
done
```

No Foundry? The same read is one JSON-RPC call. `getAttestation(bytes32)` has selector `0xa3112a64`, so its calldata is the selector followed by the 32-byte UID:

```bash
curl -s "$RPC" -H 'content-type: application/json' --data \
 '{"jsonrpc":"2.0","id":1,"method":"eth_call","params":[{"to":"'"$EAS"'","data":"0xa3112a64'"${UID#0x}"'"},"latest"]}'
```

The `data` member of the returned struct is the ABI-encoded ScanRecord above. Hand it to any ABI decoder with the tuple `(bytes32,string,string,string,uint32,string,uint8,uint8,bytes32)` and you get the same nine fields, straight off the chain, with nothing of ours in the path.
