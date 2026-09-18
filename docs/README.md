# Docs site (Astro Starlight)

This folder (`docs/`) is the documentation website source. It is built with
[Astro](https://astro.build) + the [Starlight](https://starlight.astro.build)
docs theme.

## Run locally

Requirements: Node.js >= 22.12.0 (Astro v7 requirement).

```bash
cd docs
npm install
npm run dev
```

Then open the printed local URL (usually http://localhost:4321).

## Build locally (same check CI runs)

```bash
cd docs
npm install
npm run build
```

The static site is emitted to `docs/dist/`. Preview it with:

```bash
npm run preview
```

## GitHub Pages

For a project page (`https://<org>.github.io/<repo>/`), build with:

```bash
cd docs
ASTRO_BASE=/<repo> npm run build
```

The generic workflow in `.github/workflows/deploy-pages.yml` builds and publishes
this site to GitHub Pages automatically on every push to `main`/`master` that
touches `docs/` (enable Pages once: Settings → Pages → Source: "GitHub Actions").

## Where content lives

- Landing page: `src/content/docs/index.mdx`
- Layer overviews: `src/content/docs/{asw,cdd,services,ecu-abstraction,mcal,communication,memory,diagnostics,crypto,system,tools}/index.mdx`
- Canonical module pages: `src/content/docs/modules/<layer>/<Module>.mdx`
- Vector SIP hub: `src/content/docs/sip/` (SIP overview + per-module SIP notes linking back to canonical pages)
- Converted delivery documents: `src/content/docs/general/…` and
  `src/content/docs/modules/<layer>/<Module>/…` (each converted file states its
  source PDF and extraction limits)
- Site config/sidebar: `astro.config.mjs`

## Conventions

- Every module page carries an **origin badge**: Vector-provided,
  Vector-provided (Ford-customised), Third-party via Vector, or Custom.
- Code references are relative to the repository root (e.g. `BSW/Can/Can.h`).
- Converted PDFs are summaries plus extracted excerpts; the authoritative
  document remains the original PDF in `Doc/` (linked from each page).
