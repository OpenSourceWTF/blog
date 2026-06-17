---
title: "The Load-Bearing Internet"
date: "2026-06-17"
excerpt: "A handful of tiny, mostly unpaid software projects hold up almost everything you use online. We built a live leaderboard to show which ones, and why that same handful is what attackers go after."
tags: ["announcement", "leaderboard", "supply-chain", "security", "open-source"]
---

# The Load-Bearing Internet

*Introducing the Open Source Leaderboard — live at [leaderboard.opensource.wtf](https://leaderboard.opensource.wtf)*

![All modern digital infrastructure — a precarious tower of blocks balanced on one small piece labeled "a project some random person in Nebraska has been thanklessly maintaining since 2003."](https://imgs.xkcd.com/comics/dependency_2x.png)

<small>Randall Munroe, [xkcd #2347 "Dependency"](https://xkcd.com/2347/) — [CC BY-NC 2.5](https://creativecommons.org/licenses/by-nc/2.5/). The whole post in one drawing.</small>

In 2016, a programmer got into a fight with a company and, in protest, deleted a tiny piece of code he'd shared online. It was called `left-pad`. All it did was add a few blank spaces to the front of a line of text. Eleven lines long. Nothing you'd think about twice.

Within a few hours, builds were failing all over the world. Apps people used every day, tools from enormous companies, projects with millions of users: the code behind them suddenly wouldn't build. That eleven-line scrap was buried deep inside thousands of other projects, and when it vanished, they came apart.

That wasn't a freak accident. It's roughly how all modern software gets built.

## The internet is a tower of borrowed parts

Almost nobody writes software from scratch anymore. When someone builds an app, they don't write every piece by hand. They reach for ready-made building blocks that other people have written and given away for free. These blocks are called *packages*, and a single app can rely on hundreds or thousands of them without anyone noticing.

Each block usually sits on top of more blocks, which sit on more blocks, all the way down. Stack it up and you get the tower in the comic above: a giant, wobbly pile labeled "all modern digital infrastructure," resting on a few small pieces near the bottom.

What the comic gets right is who's holding those pieces up. A surprising number of them are looked after by one person, the *maintainer*, working for free, on their own time, with no company behind them. The internet you lean on every day is held up, in part, by unpaid volunteers most people have never heard of.

## Downloads don't measure what matters

For most people, this layer of the internet is invisible. That's why we built the **Open Source Leaderboard**: a live view of the open-source world that updates as developers publish new code.

Most package lists rank by a single number, usually raw downloads. We use three, because any one number on its own is misleading.

The simplest one is the **dependent count**: how many other packages list this one as a building block. A high count means many projects would feel it if this package broke. But it only sees the projects sitting *directly* on top of a package. It misses the ones sitting on something that's sitting on it.

To catch those, we borrow the idea that made Google work. Google ranks a web page higher when other important pages link to it, weighing the important ones more heavily than the rest. We score packages the same way: a package's **influence** climbs when important packages depend on it, and those packages earned their importance the same way, on down the chain. (The technical name is Katz centrality, a close relative of Google's PageRank, run across the web of dependencies.) That math finds the hidden heroes: a small package with an unremarkable dependent count can still rank high, because the giants depending on it are load-bearing themselves.

The third ranking isn't ours. The **[OpenSSF Criticality Score](https://github.com/ossf/criticality_score)** is a public standard from the Open Source Security Foundation that estimates how critical a project is to the software world. It mixes in signals like how widely a project is used, how actively it's kept up, and how long it's been carrying weight. We fold it in as a second opinion from outside the project.

Together they let you sort the tower, heaviest pieces first.

## The same view shows what to protect first

A handful of packages carry far more weight than the rest. That concentration is the most fragile part of the supply chain. Knowing which packages everything depends on tells you which ones to protect first: the few that most deserve funding, extra maintainers, security review, and a closer watch. You can't protect a package you never knew was holding everything up.

These same packages are what attackers go looking for, which is why they're worth guarding most.

## The break-in moved into the supply chain

For years, breaking into a company meant getting through its firewall and into its servers. Companies spent fortunes hardening that front door, and it worked well enough that attackers stopped knocking. They found a better way in: the tower itself.

You don't have to attack the top of the tower. You poison a block near the bottom and let everything stacked above it carry your code upward for you. Tamper with one widely-used package, and the next time a developer installs or updates it, the bad code rides in next to the real thing, into their app and every app built on theirs. This is a *supply-chain attack*. The web of dependencies that makes software quick to build also carries a poisoned package into thousands of projects on its own.

## The big packages got hijacked, one after another

Over the past year and a half this stopped being rare. Each wave was smarter than the last.

**March 2025: tj-actions.** Tens of thousands of projects leaned on one small tool to run their automated builds. An attacker rewrote it to dump the memory of the machine building the code into logs anyone could read, scattering passwords, cloud keys, and access tokens in the open. It started as a targeted hit on the crypto company Coinbase and spread to more than 23,000 projects before researchers caught it.

**September 2025: chalk and debug.** An attacker emailed a fake security warning to the maintainer of some of the most-downloaded packages on npm, the storehouse most JavaScript projects pull their code from. The maintainer clicked, lost his account, and poisoned versions went out under his name. Eighteen packages were hit, together downloaded about 2.6 billion times a week. The planted code watched for crypto payments and quietly rerouted them to the attacker. It was caught within hours, but at those download counts, hours were plenty.

**September 2025: Shai-Hulud.** Days later came the first npm worm that spread on its own. Once it landed on a developer's machine, it stole their saved keys and used them to poison every package that developer published. Those poisoned packages carried it onto the next machine, and the next. It hit more than 500 package versions and copied the secrets it stole into public view before researchers shut it down.

**November 2025: Shai-Hulud 2.0.** The worm came back bigger. It booby-trapped 796 packages, downloaded more than 20 million times a week, and ran its code at the start of installation, before security scanners had a chance to catch it. It swept up maintainers at companies like Zapier, PostHog, and Postman.

**2026: still spreading.** The build-memory trick from a year earlier turned up again in poisoned releases from SAP and Bitwarden. In May, a smaller copycat worm hit more than 170 packages, and for the first time it crossed languages, into PyPI, the Python world's version of npm.

## The most important computer is unprotected

This is why the load-bearing few are the prize. A package that thousands of others sit on is a distribution network: one upload reaches every project downstream. And the keys that publish it don't live in a guarded data center. They sit on the maintainer's laptop.

We put enormous effort into protecting *production*, the live servers out in the world, with firewalls and monitoring and locked-down access. We guard them like a vault.

And then we treat the developer's laptop like a desk to type at.

That gap, between how hard we guard the servers and how little we guard the machine where software is made, is where we're going next.

The leaderboard is live today, and like everything here, it's free: go see what's holding your own software up at [leaderboard.opensource.wtf](https://leaderboard.opensource.wtf).

---

## Under the hood: how the three rankings work

*The rest of this is the math behind the three numbers. Skip it if you want to browse the board; the code is open if you'd rather read that.*

### 1. Dependent count

This is the base metric. For every package we pull its dependent count from [ecosyste.ms](https://ecosyste.ms): the number of other packages in the same ecosystem (npm or PyPI) that list it as a dependency. No weighting, no math, just a count. It's the default sort on the board: if this package vanished tomorrow, how many projects would notice right away?

But it's flat. A package depended on by ten throwaway projects and one depended on by ten of the most important libraries in the world can show the same number. The next ranking fixes that.

### 2. Influence (truncated Katz centrality)

Influence treats the ecosystem as a graph: every package is a dot, and every dependency is an arrow between two dots. We want a score that counts who depends on you, weighing important packages more heavily than minor ones. That is what Katz centrality measures.

We compute a truncated version, per ecosystem, over the top packages (by default the top 500 by dependent count):

```
base(p)      = log1p( dependent_count(p) )            // direct reach, log-damped
influence(p) = Σ over d = 0..K of  αᵈ · (Rᵈ · base)[p]
               with  α = 0.15,  K = 3
```

Walking through it:

- **`base`** gives every package a starting score from its direct dependents. We take the logarithm so that a few giant packages don't drown out everyone else.
- **`R`** is the *reverse* dependency graph. Multiplying by it (`R · base`) replaces each package's score with the sum of the scores of all the packages that depend on it. Do it again (`R²`) and you reach the packages that depend on *those*, and so on outward.
- **`α = 0.15`** shrinks each extra hop, so a package that depends on you directly counts far more than one three steps away. We stop after **`K = 3`** hops, because by then `α³ ≈ 0.003` and deeper terms barely move the number.
- Finally we scale every score into a 0-to-1 range.

We build the reverse graph by fetching each package's *forward* dependencies straight from the registries (npm's `dist-tags.latest.dependencies`, PyPI's `requires_dist`, skipping optional extras) and inverting them. The registries are the only practical source: deps.dev exposes the reverse graph only through BigQuery, and its forward API is tied to specific versions. Arrows that point outside the top set are dropped, so influence re-ranks *within* that set instead of claiming to score the whole ecosystem.

In practice, packages like npm's `semver` or PyPI's `typing-extensions` rank near the top despite a middling raw dependent count.

### 3. OpenSSF Criticality Score

We don't compute the third ranking ourselves. The [OpenSSF Criticality Score](https://github.com/ossf/criticality_score) is an open, published standard from the Open Source Security Foundation. It rates a project from 0 to 1 by combining repository signals like how old it is, how recently and how often it's updated, how many people and organizations contribute, and how many other projects depend on it. The foundation republishes the full dataset, a CSV of hundreds of thousands of repositories, on a regular schedule.

We download the latest CSV and use [DuckDB](https://duckdb.org) as a local query engine to read it and match it to our packages by repository URL. (Both sides get normalized first: lowercased, with `https://`, `www.`, `.git`, and trailing slashes stripped off, so the URLs line up.) This score is *direct*, not transitive: it rates each project on its own, with no graph walk. We show it next to our own influence number as an independent signal from outside the project.

---

## Built on the community's work

The leaderboard doesn't generate any of this data. It stitches together work the open source community already does and puts it in one place. We're grateful to the people and projects behind it:

- **[ecosyste.ms](https://ecosyste.ms)** for open package metadata and dependent counts across npm, PyPI, and dozens of other ecosystems.
- The **Open Source Security Foundation** and its **[Criticality Score](https://github.com/ossf/criticality_score)**, an open, published measure of how critical a project is.
- The **[npm registry](https://www.npmjs.com)** and **[PyPI](https://pypi.org)** for the public package and dependency data we read.
- **[DuckDB](https://duckdb.org)** for querying and joining the criticality dataset locally, with no data warehouse needed.
- The network-science research behind **[Katz centrality](https://en.wikipedia.org/wiki/Katz_centrality)** and **[PageRank](https://en.wikipedia.org/wiki/PageRank)**, which the influence score borrows from.
- **Randall Munroe** for [xkcd #2347](https://xkcd.com/2347/), which says it better than we could.

We're also built on countless other open source tools, far too many to name. Most of all, we're grateful to the maintainers, most of them unpaid, who keep the load-bearing packages running. This whole project is just a way to point at your work. Thank you.
