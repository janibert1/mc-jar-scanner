# Solar JAR Scanner

Live at **https://jarscan.jdries.nl**

A free, client-side scanner for the "Solar" JAR infector — an undocumented
malware campaign that hit at least 5 German Minecraft server networks
between Feb–Aug 2026, first publicly documented by
[u/Plattio's writeup](https://gist.github.com/Plattio/d40f53c2e9f962746f3c01a1a244b9e4).
No existing scanner caught it at the time of writing (confirmed via
[MCAntiMalware issue #778](https://github.com/OpticFusion1/MCAntiMalware/issues/778)).

## Why this exists

The original writeup gives exact manual detection commands, but running
them means extracting every JAR by hand and grepping the results. This
page automates that: drop your plugin/server JARs in, get a clear
clean/hit result per file.

## Why it's safe to actually use

Everything happens in your own browser. There is no backend — this repo
is the entire product: `index.html` + a vendored copy of
[JSZip](https://github.com/Stuk/jszip) (MIT license) to read `.jar`
(zip) contents client-side. Open devtools' Network tab while using it —
nothing goes out except the initial page load.

## What it checks

Straight from the original writeup:

1. **Filename check** — a zero-width-space (U+200B) appended to a
   `./logs` entry name, the loader's persistence trick.
2. **Signature check** — four base64-encoded constant-pool strings that
   only appear once a class file is actually extracted (a plain `grep`
   on the `.jar` itself finds nothing, since the classes are
   DEFLATE-compressed).

## What it does NOT check

This is a signature check against one specific, documented campaign —
not a general malware scanner. It won't catch a variant that changes
its strings, and it doesn't implement the writeup's deeper "Layer 2"
structural bytecode analysis (nop-padding, shifted debug tables). A
clean result is real evidence, not a guarantee.

## Deploy

Static files, no build step. `index.html` + `jszip.min.js` served as-is;
`jarscan.jdries.nl` runs it behind an nginx + Cloudflare Tunnel setup,
nothing specific to that infrastructure is required to self-host it
elsewhere.

## Credit

All detection logic and the original research: [Plattio](https://gist.github.com/Plattio/d40f53c2e9f962746f3c01a1a244b9e4).
This repo just automates the check.
