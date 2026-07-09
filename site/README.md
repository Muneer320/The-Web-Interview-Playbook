# Playbook site

Astro renders the [Markdown guide](../web-dev-interview-playbook/README.md) as static lessons, React practice tools and Pagefind search. The site does not modify the guide or send a learner's stories/attempts to a backend.

## Local setup

Requires Git and Node24+ (see `.node-version` and `package.json`). The refreshed Astro toolchain exceeds the former Node22.12 baseline; local checks and CI use Node24. From the repository root:

```sh
cd site
npm ci
npm run test
npm run build
npm run preview
```

Open http://localhost:4321. `npm run build` creates `dist/` and its Pagefind index; `npm run preview` uses `scripts/serve-dist.mjs` so post-build search assets are served. `PORT` changes the preview port. The development server is `npm run dev`; search is unavailable until a production build. The preview is a development utility, not a hardened public application server.

## Source and browser state

`src/content.config.ts` glob-loads the guide. `astro.config.mjs` applies checklist, reveal, prediction and Markdown-link transforms. React widgets are placed through `src/widgets/manifest.ts` and `WidgetHost.tsx`; see [authoring notes](AUTHORING.md) for their contracts.

STAR stories and mock attempts are read from localStorage on client mount. Those two pages use `client:only="react"` with a loading fallback, rather than hydrating static empty-state markup against saved browser data. Content explanations and guide links remain available in the generated HTML. Clearing storage removes local practice history; story text can be exported. Private data should not be used for testing or screenshots.

Local verification covered the existing unit suite, complete static build/index, real browser search, prediction locking, roadmap/theme persistence, synthetic story save/export and saved mock-attempt reload. Desktop/mobile widths were checked. It is not a full editorial review of the guide or all widget models.

## Deployment boundaries

`dist/` is the complete static output. Optional `ASTRO_SITE` sets the canonical origin and `ASTRO_BASE` sets a deployment subpath, both read at build time. For this repository's Pages destination use origin `https://muneer320.github.io` and base `/The-Web-Interview-Playbook`.

GitHub Pages is configured to publish this repository's **gh-pages branch, root directory**. Retain `.nojekyll` alongside generated output. Preserve the branch history and publish a newly built subpath bundle; do not push source code into that branch. The main verification workflow runs tests/builds but does not deploy.

The existing `staging` branch belongs to an earlier collaborator demo configuration with the Om-Midya origin and a GitHub Actions deployment workflow. It is retained unchanged; do not merge its deployment workflow into main or assume it controls this repository's Pages settings.

Full Git history enables last-change badges (`fetch-depth: 0` in the verification workflow). Shallow builds skip those badges. A date badge is not a technical-content correctness certification. Contributor credits and the absence of a project-wide reuse license are recorded in the [root README](../README.md).
