# LANScan dashboard

The live dashboard is at:

> ### https://neohiro.github.io/lanscan/

This file records where it lives, where its source lives, and why the obvious
alternatives do not work — because all three of those were tried first.

## The dashboard and the scanner are different things

| | Where |
|---|---|
| Scanner (the thing that sends ARP/ICMP) | this repository — `LANSCAN.PY`, releases as standalone builds |
| Dashboard (the browser view) | [`neohiro/neohiro.github.io`](https://github.com/neohiro/neohiro.github.io), under `lanscan/` |

The split is forced by the browser, not by preference: a web page is sandboxed
and cannot open a raw socket. The dashboard renders a snapshot the scanner
serves over loopback, so **the page alone shows nothing** — you need the
standalone build running first. The dashboard has a per-platform download row
for exactly this reason.

## Why the dashboard source is not in this repository

Two reasons, and the second is the one that used to bite.

**GitHub serves a repository named `<name>.github.io` from
`https://<owner>.github.io/<name>.github.io/`.** A repo called
`lanscan.github.io` therefore *cannot* serve `/lanscan/` — the path it appears to
own. It never could. This repository was briefly named that way, which produced
a stub whose only job was to redirect.

**Two copies of a live dashboard is two that drift.** The dashboard's privacy
gate, its schema and its renderer change together; a copy in a second repository
means a fix lands in one and the other keeps serving the old behaviour with no
signal that it is stale.

## Why the bare domains are not here

Both `lanscan.github.io` and `lan.github.io` are owned by other GitHub accounts.
GitHub reserves every `<account>.github.io`, so neither can be claimed by this
organisation regardless of repository naming.

| Requested | Reality | What is live instead |
|---|---|---|
| `lan.github.io` | owned by GitHub user `lan` | `neohiro.github.io/lanscan/` |
| `lanscan.github.io` | owned by GitHub user `lanscan` | `neohiro.github.io/lanscan/` |
| `neohiro.github.io/lan` | free, superseded | `neohiro.github.io/lanscan/` |
| `neohiro.github.io/lanscan` | **available** | **canonical** |

## The privacy gate

The dashboard makes no third-party request on load. That is the product, not a
nicety, and it is enforced rather than promised:

```
neohiro.github.io → lanscan/scripts/check-selfcontained.mjs   the gate
neohiro.github.io → lanscan/scripts/test-selfcontained.mjs   21 tests on the gate
```

It rejects `<script src>`, `<img src>`, `<link href>`, `@import` and `fetch()`
pointing anywhere but `127.0.0.1` and this site's own origin. It permits an
`<a href>` to a release download, because that fetches nothing until the visitor
clicks it — a distinction that is per-attribute, so a `<link>` to the same host
is still rejected.

Both run as a required CI job. If you change anything under `lanscan/`, they are
what will tell you the page started phoning home.