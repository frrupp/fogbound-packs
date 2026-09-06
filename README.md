# Fogbound region packs

Neighbourhood and district data for the [Fogbound](https://frrupp.github.io/fogbound-site/)
app, served as static JSON over GitHub Pages.

## Why these are not in the app

**Nothing ships inside the app.** Berlin and Hamburg used to, on the argument
that it should work on first launch with no network; that lost to a better one,
that a region is dead weight for everyone who does not live in it. Dropping both
took the JS bundle from 6.40 MB to 4.14. A new user opens Progress, is told
there is no map for where they are, and is offered the region that covers them —
the screen everyone outside Berlin already saw, now the screen everyone sees.

Baden-Württemberg settles it on its own: **23.9 MB**, which cannot be trimmed
because boundaries are 16 of those megabytes and they are the data. A pack only
one state's residents need is exactly what should not ship to everybody.

## Why they are not in the site repo

`fogbound-site` is four small files and a privacy policy, and its history should
stay that way. Packs are bulk: each rebuild adds another copy that git keeps
forever. Here that is expected, and this repo can be discarded and recreated
without touching the site.

The **manifest stays on the site** — `fogbound-site/regions/index.json` — because
it is the one URL a shipped app version has baked in and can never move. It
carries absolute pack URLs, so everything here can move host at any time.

## Why Pages rather than Releases

Pages serves `content-encoding: gzip` and release assets do not. This data
compresses about 4.5×: Baden-Württemberg is 23.9 MB on disk and **5.32 MB over
the wire**. That is the difference between a download people accept and one they
cancel.

Pages picks its own compression level, and it is not the `gzipSync` level 9 the
manifest measures with — it lands about 3% larger. Inside the rounding a "5 MB"
label does, and worth knowing rather than assuming: the manifest is written
before the packs are pushed, so it cannot measure the thing it describes.

## Building a pack

In the app repo:

```bash
node scripts/build-boundaries.mjs baden-wuerttemberg --count   # levels, before anything expensive
PROBE=Stuttgart node scripts/build-boundaries.mjs baden-wuerttemberg --dry
node scripts/build-boundaries.mjs baden-wuerttemberg           # writes dist/packs/
node scripts/build-manifest.mjs --out ../fogbound-site/regions/index.json
```

The manifest is **generated, never hand-edited**: it carries a `wire` figure
measured by compressing each pack, and typing that by hand is how a download
button comes to overstate its cost fivefold.

Downloadable packs are written to a gitignored `dist/packs/` rather than
`assets/boundaries/`, so a 24 MB file can never be committed to the app or
`require`d into its bundle by accident.
