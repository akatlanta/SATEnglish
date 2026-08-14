# SAT English Practice

A free, single-page practice site for the digital SAT Reading & Writing section. No build step, no dependencies — just one self-contained `index.html`.

## What's inside

**Practice by Category** — 60 original questions across 8 sets, covering all four SAT Reading & Writing domains:

- **Information and Ideas** — Central Ideas & Details, Command of Evidence, Inferences
- **Craft and Structure** — Words in Context, Text Structure & Purpose, Cross-Text Connections
- **Expression of Ideas** — Rhetorical Synthesis, Transitions
- **Standard English Conventions** — Boundaries, Form/Structure/Sense

**Full Mixed Practice** — 5 independent 60-question practice tests (300 questions total), each shuffled across all four domains and covering completely different content:

- **Set 1** — the original 60 category questions above
- **Set 2** — natural sciences & the environment
- **Set 3** — history & social studies
- **Set 4** — arts, literature & language
- **Set 5** — technology, business & everyday life

Each question gives instant right/wrong feedback with an explanation, and each set ends with a score summary and full answer review. Answer-choice order is randomized on every page load so the correct answer isn't predictably in the same position.

**Note:** scores/progress are session-only (kept in memory) and reset on page reload — there's no backend or database.

## Running it locally

Just open `index.html` in any browser. No server or build tools required.

## Deploying

This is a static site, so it can be hosted anywhere that serves static files:

- **Netlify** — drag and drop `index.html` at [netlify.com/drop](https://app.netlify.com/drop), or connect this GitHub repo in the Netlify dashboard for auto-deploys on every push.
- **GitHub Pages** — in this repo's Settings → Pages, set the source to the `main` branch / root, and GitHub will publish it at `https://<username>.github.io/<repo-name>/`.
- **Vercel** — import this repo in the Vercel dashboard; no build command needed (framework preset: "Other").

## Adding more questions

Each question is a plain JS object:

```js
{ set:'s1', tag:'Central Ideas & Details',
  passage:`...`,          // string, or {a:`...`, b:`...`} for a two-text Cross-Text Connections question
  stem:`...`,
  choices:[`...`,`...`,`...`,`...`],
  correct:0,               // index (0-3) of the correct choice
  explanation:`...` }
```

**Category sets:** live in the `QUESTIONS` array (search for `QUESTIONS.push`). Push more objects with an existing `set` id (`s1`–`s8`) to add to that set — the site groups and counts them automatically. To add a new category set, add an entry to the `SETS` array and give your new questions that set's `id`.

**Mixed practice sets:** `MIX2`, `MIX3`, `MIX4`, `MIX5` are separate 60-item arrays (search for `const MIX2 =`, etc.), each with all objects tagged `set:'mix2'` / `'mix3'` / etc. `mix1` isn't a separate array — it's just the original 60 `QUESTIONS`. To add a 6th mixed set: create a new `MIX6` array, add it to `MIX_POOLS`, and add a matching entry to `MIXED_SETS`.
