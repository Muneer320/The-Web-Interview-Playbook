# Authoring the interactive Playbook

From the repository root, the guide lives in `web-dev-interview-playbook/` and the renderer in `site/`. See the [site notes](README.md) for verified commands and [contribution guide](../web-dev-interview-playbook/CONTRIBUTING.md) for content conventions.

## How pages come to exist (and how to add one)

The site glob-loads the guide as an [Astro content collection] — **markdown in,
pages out**. There is no per-page wiring.

### Add a document to an existing section

Drop a `.md` file into the section's folder (e.g.
`web-dev-interview-playbook/04-react/my-new-topic.md`). That's the whole
process: it gets a page, appears in the sidebar and the section's doc list,
is indexed for search, and its checklists/questions pick up interactivity
automatically if they follow the conventions below.

### Add a whole new section

Create a numbered folder with a `README.md`
(e.g. `web-dev-interview-playbook/13-company-questions/README.md` plus docs).
New sections self-register: navigation, search, progress tracking, and a
section index page all appear without touching the site code.

### Conventions that unlock interactivity

The build-time transforms key off ordinary markdown patterns:

| You write… | The site renders… |
| --- | --- |
| `- [ ] task` checklists | Persistent checkboxes + progress rings |
| `### Question` / `**Answer:**` / `**Follow-ups:**` in an `interview-questions.md` | Think-first reveal cards |
| `**Answer:** … **Why:** …` in a `tricky-output-questions.md` | Type-a-prediction gates (answer locked until the reader commits a guess) |
| The `ROADMAP.md` week tables | The interactive planner at `/roadmap/` |

### Add an interactive widget (site code)

Widgets follow a strict three-step recipe, enforced by tests:

1. Create the component in `site/src/widgets/<name>/` (default export). Most
   widgets ride an existing engine — `Stepper` (hand-authored state traces),
   `PlaygroundFrame` (controls → live preview), or `Simulation` + `topo-svg`
   (deterministic tick-based animations). Hand-authored trace/model data gets
   its own `.ts` file with semantic unit tests.
2. Add one `lazy()` line to the `REGISTRY` in `site/src/widgets/WidgetHost.tsx`.
3. Add one metadata entry to `SECTION_WIDGETS` in `site/src/widgets/manifest.ts`
   (which section page shows it, title, description).

Drift-guard tests fail if a widget is registered but unplaced or placed but
unregistered, and if any manifest key isn't a real section folder. One rule of
thumb from this codebase: the teaching data is the product — model semantics
and trace notes are tested against real behavior, not just structure.

### Add a standalone tool page

Tool pages (like `/star-builder/`) are Astro pages that parse guide documents
at build time and pass the data to a React island:
`site/src/pages/<tool>.astro` + a widget folder + a card entry in
`SECTION_TOOLS` (`site/src/widgets/manifest.ts`) so the section page links it.
Parsers live in `site/src/lib/` and must fail soft — a malformed source doc
renders a link to the original, never a broken page.

### Two house rules for any new page or component

- **Internal links go through `withBase()`** (`site/src/lib/base.ts`) — never a
  raw `href="/…"`. This keeps the site deployable at a domain root *or* under a
  subpath (the staging deploy checks for leaks and fails loudly on violations).
- **Styling uses the design tokens** in `site/src/styles/tokens.css`
  (light "playbook paper" / dark "chalkboard"). Token names are a contract —
  retune values freely, never rename.


[Astro content collection]: https://docs.astro.build/en/guides/content-collections/
