# SAT English Practice

A free, single-page practice site for the digital SAT Reading & Writing section. No build step, no dependencies — just one self-contained `index.html`.

## What's inside

60 original practice questions across 8 sets, covering all four SAT Reading & Writing domains:

- **Information and Ideas** — Central Ideas & Details, Command of Evidence, Inferences
- **Craft and Structure** — Words in Context, Text Structure & Purpose, Cross-Text Connections
- **Expression of Ideas** — Rhetorical Synthesis, Transitions
- **Standard English Conventions** — Boundaries, Form/Structure/Sense

Plus a "Full Mixed Practice" mode that shuffles all 60 questions together.

Each question gives instant right/wrong feedback with an explanation, and each set ends with a score summary and full answer review.

**Note:** scores/progress are session-only (kept in memory) and reset on page reload — there's no backend or database.

## Running it locally

Just open `index.html` in any browser. No server or build tools required.

## Deploying

This is a static site, so it can be hosted anywhere that serves static files:

- **Netlify** — drag and drop `index.html` at [netlify.com/drop](https://app.netlify.com/drop), or connect this GitHub repo in the Netlify dashboard for auto-deploys on every push.
- **GitHub Pages** — in this repo's Settings → Pages, set the source to the `main` branch / root, and GitHub will publish it at `https://<username>.github.io/<repo-name>/`.
- **Vercel** — import this repo in the Vercel dashboard; no build command needed (framework preset: "Other").

## Adding more questions

All question data lives in the `QUESTIONS` array inside `index.html` (search for `QUESTIONS.push`). Each question is a plain JS object:

```js
{ set:'s1', tag:'Central Ideas & Details',
  passage:`...`,          // string, or {a:`...`, b:`...`} for a two-text Cross-Text Connections question
  stem:`...`,
  choices:[`...`,`...`,`...`,`...`],
  correct:0,               // index (0-3) of the correct choice
  explanation:`...` }
```

Push more objects with an existing `set` id (`s1`–`s8`) to add questions to that set — the site groups and counts them automatically. To add a new set entirely, add an entry to the `SETS` array and give your new questions that set's `id`.
