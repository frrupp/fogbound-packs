# Fogbound region packs

Neighbourhood and district data for the [Fogbound](https://frrupp.github.io/fogbound-site/)
app, served as static JSON over GitHub Pages.

## Why these are not in the app

Berlin (1.4 MB) and Hamburg (1.0 MB) ship inside the app, because it has to work
on first launch with no network. **Baden-Württemberg is 22 MB.** Bundling that
would take the app from 61 MB to about 85 for everyone, including the people who
will never open it — and it cannot be trimmed, because boundaries are 16.7 of
those 22 MB and they are the data.

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
compresses about 4.5×: Baden-Württemberg is 22.4 MB on disk and **4.96 MB over
the wire**. That is the difference between a download people accept and one they
cancel.

## Building a pack

In the app repo:

```bash
node scripts/build-boundaries.mjs baden-wuerttemberg   # writes dist/packs/
```

Downloadable packs are written to a gitignored `dist/packs/` rather than
`assets/boundaries/`, so a 22 MB file can never be committed to the app or
`require`d into its bundle by accident.
