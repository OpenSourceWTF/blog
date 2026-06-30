---
title: "Caught at Publish Time"
date: "2026-06-29"
excerpt: "About 85,000 packages ship to npm and PyPI every day, more than anyone can read. So an AI reads them for us, a sandbox runs them, and the verdicts go somewhere they can't be erased."
tags: ["announcement", "leaderboard", "supply-chain", "security", "scanning", "malware", "open-source"]
---

# Caught at Publish Time

*About 85,000 packages ship to npm and PyPI every day. We read each one with an AI, run it in a sandbox, and write the verdict where it can't be quietly erased.*

[Last time](https://opensource.wtf/blog/launch-the-load-bearing-internet) we ended on a gap. We spend a fortune guarding production, the servers out in the world, and almost nothing guarding the machine where the software actually gets built. The supply chain runs right through that machine. Every `npm install` pulls code from someone you've never met and runs it on your laptop, next to your keys and your tokens.

The leaderboard could tell you which packages the world leans on. It couldn't tell you if one of them had gone bad. This is the part that can. It reads new releases, runs them, and flags the ones acting like malware, fast enough to catch a poisoned version while it's still spreading. The verdicts go on a public blockchain, so they stick around even if we don't.

## The flood is too fast to read by hand

Open the **Live Feed** and watch it for a minute.

![The Live Feed: a stream of npm and PyPI packages publishing in real time, each row stamped with how long ago it landed, the newest seven and nineteen seconds old.](/content/blog/images/feed-stream.png)

<small>The live publish stream. The numbers on the right are how long ago each version shipped.</small>

Packages land every few seconds. Our feed counts about 3,500 an hour across npm and PyPI. Call it 85,000 a day. Nobody reads 85,000 packages a day, and mostly nobody reads them at all. A maintainer publishes, it's live in seconds, and the first person to look hard at the code is usually whoever it robs.

You can't hire your way out of that. There's no review to sneak past because there is no review. So we handed the job to a machine. We're not caught up to the whole firehose yet, so it starts with the packages the leaderboard already ranks, the load-bearing ones, and takes new releases off the feed as they come in. Each package it gets to is looked at twice. An AI reads the source. A sandbox installs it and watches what it tries to do, and a second AI reads back what the sandbox saw. Then a set of deliberately paranoid rules decides whether any of it adds up to malware. The rules are strict on purpose, because the quickest way to make a scanner worthless is to let it cry wolf.

## An AI that reads the code beats a scanner that matches patterns

The usual way to scan a package is to check it against a list of things already known to be bad: a string, a function name, the fingerprint of malware someone caught last month. It's quick, and it only ever finds what's already on the list. New tricks aren't on the list.

An AI that reads the code can catch a trick the first time it sees one. Here's one it caught.

![The load-dotenv package page, marked High-risk. The static read flags an implicit-environment-injection and a supply-chain-persistence finding, while the behavioral sandbox came back Clean.](/content/blog/images/load-dotenv-highrisk.png)

<small>`load-dotenv` is named to be mistaken for `python-dotenv`. The AI read the source and found the trap; the sandbox install came back clean.</small>

`load-dotenv` is named to get mistaken for `python-dotenv`, the package much of the Python world uses to read config files. Install it and nothing obviously bad happens. What the model noticed in the source is a `.pth` file, one of those quiet hooks Python runs on its own every time it starts up. This one was rigged to fire on every Python process on the machine, pulling in environment variables an attacker could point wherever they wanted. The package does nothing at install. It arranges to keep doing something later, on every Python session from then on.

A pattern scanner reads that as an ordinary package and moves on. The sandbox installed it and saw nothing, because the trap doesn't spring until some later run. Reading the code was the only thing that caught it.

## We catch it while the worm is still spreading

Reading them is half of the job. Doing it in time is the rest. The scanner takes its work off that same publish feed, so most releases get scanned within an hour of going up, not three weeks later in somebody's writeup.

![The Scans dashboard: 16,214 package versions scanned, 120 flagged, a live queue, and two packages being scanned at this moment.](/content/blog/images/scan-dashboard.png)

<small>The Scans tab. The bar is every version checked so far, sorted by verdict; the left column is the live queue.</small>

A verdict on a version less than three days old gets a flag on it, because the dangerous moment for a poisoned release is right after it ships and before anyone's noticed. The npm worms this past year each hit hundreds of packages inside a day. On the board that shows up as a sudden rash of brand-new high-risk versions, going up while the thing is still hopping from laptop to laptop. That's the window where a warning is worth anything at all.

## The verdicts live on a blockchain so they outlast us

A verdict isn't much use if you can't tell whether it's been quietly changed since. So every finished one also gets written to the **TEA blockchain**.

![The per-version view for a flagged package, showing an on-chain record of the high-risk verdict with a signed payload and a link to the transaction.](/content/blog/images/aidguard-onchain.png)

<small>Each verdict is attested on-chain: signed, dated, and linked to a transaction anyone can pull up, sitting apart from our own database.</small>

Our database can go down. The site can get pulled offline, or sued offline, or just outlast our interest in running it. The chain doesn't care. Once a verdict is written there it's a signed, dated record on a ledger anyone can read, with us or without us. We can't go back and edit it, and we can't quietly walk one back to cover a mistake. For a record whose only job is to say "this version was malware on this date," that permanence is the whole reason to bother with a chain. Click any verdict and you can pull up the transaction yourself.

## Everything the scanner found, on one package

Here it all is on a single package. `aid-guard1` is listed as a dependency by 7,514 others. It gets downloaded 140 times a week. Those two numbers do not belong together, and the gap between them is the first thing wrong with it.

![The aid-guard1 detail page, marked High-risk for dependency-confusion and typosquatting, with the AI's report and a critical finding on the dependency list.](/content/blog/images/scan-highrisk.png)

<small>`aid-guard1`: 7,514 dependents, 140 downloads a week, and a dependency list built to be installed by accident.</small>

At the top of its page is the verdict, **High-risk**, with the reason beside it: dependency-confusion and typosquatting. Under that, the two checks sit side by side, the read and the sandbox run, each with its own result.

Below them is the AI's account in plain words. `aid-guard1` has a harmless-looking name and a dependency list packed with randomly generated junk package names. Match any one of those names to a package some company uses internally, and an installer can grab this public version instead, payload and all. A hundred junk names is a hundred chances at it. The specific finding sits under the writeup, pinned to the line in the manifest, with the nonsense names quoted straight out of the source.

Off to the side are the numbers the leaderboard runs on, rank and dependents and influence, so you can see how much weight a flagged package was holding up. There's a cross-check against OSV, the public advisory databases, next to our own call. And there's the full version history, each release scored on its own, with the on-chain receipt for this one.

A maintainer can read that page and see exactly what tripped it. Someone about to install it can read the first word and stop.

## Come build the next part with us

This is a different way to watch the supply chain. Not a list of fingerprints of malware that already got someone, but something that reads and runs each package as it ships and leaves a public note of what it found. It's live, it's free like the rest of what we make, and you can go look up anything you depend on.

We're early, and the things we want to build next don't work without open-source developers in them, not just reading the announcement. If that's you, come say hi: the [TEA Discord](#), and [@opensourcewtf](#) on X. We'd rather build it with you than for you.

---

## Appendix: reading the record yourself

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
| `runNumber` | uint32 | scan run index for this version; the resolver keeps `(attester, subjectKey, runNumber)` unique |
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

No UID on hand? Derive it from the package coordinates through the resolver:

```bash
RESOLVER=0xcb78Fc94aa2fb160d3d89702b0d6078E513e1Bb8
ATTESTER=0xa25C7910208cB3690E9d1e1F44ff45927A0bBc85

# subjectKey = keccak256(abi.encode(environment, package, version))
SUBJECT=$(cast keccak "$(cast abi-encode 'f(string,string,string)' npm aid-guard1 2.7.35)")
# → 0x3da85d70…143ccab

cast call "$RESOLVER" "uidFor(address,bytes32,uint32)(bytes32)" \
  "$ATTESTER" "$SUBJECT" 0 --rpc-url "$RPC"
# → 0x0106bf55…cf8a0a
```

No Foundry? The same read is one JSON-RPC call. `getAttestation(bytes32)` has selector `0xa3112a64`, so its calldata is the selector followed by the 32-byte UID:

```bash
curl -s "$RPC" -H 'content-type: application/json' --data \
 '{"jsonrpc":"2.0","id":1,"method":"eth_call","params":[{"to":"'"$EAS"'","data":"0xa3112a64'"${UID#0x}"'"},"latest"]}'
```

The `data` member of the returned struct is the ABI-encoded ScanRecord above. Hand it to any ABI decoder with the tuple `(bytes32,string,string,string,uint32,string,uint8,uint8,bytes32)` and you get the same nine fields, straight off the chain, with nothing of ours in the path.
