# FREE SAT English Preparation 2026-27

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

**Downloadable results (Word file)** — every results screen has a **"Download Word Summary"** button. It generates a `.doc` file entirely in the browser (no server, no upload) listing every question in the set: the passage, the question, all four choices with your answer and the correct answer marked, whether you got it right, and — for anything missed — an explanation of why your choice was wrong and why the correct answer is right. It opens directly in Microsoft Word, Google Docs, or LibreOffice.

**Note:** scores/progress are session-only (kept in memory) and reset on page reload — there's no backend or database. The Word summary is a one-time snapshot generated at download time, not saved anywhere.

## Feedback / suggestions

The top bar has a **"Your Suggestions Please"** button that opens a small in-page form (name/email optional, category, message).

On submit, the form first tries **[Formspree](https://formspree.io)** — a free form-backend service. It posts directly to a Formspree endpoint (`FORMSPREE_ENDPOINT` in `index.html`, currently `https://formspree.io/f/xbgrkqgo`), and Formspree emails the submission to whoever owns that form automatically, no dashboard setup required beyond creating the form once. (We tried Netlify Forms first, but Netlify's email notifications turned out to require a paid plan — Formspree's free tier includes real email notifications, so we switched.) If the Formspree request fails for any reason (offline, endpoint unreachable, page opened as a local file), the form falls back to opening a `mailto:` draft addressed to `akatlantajan12@gmail.com` instead.

To change where submissions go: log into [formspree.io](https://formspree.io), open the form, and update its notification email in Settings — no code change needed. To point the site at a *different* Formspree form entirely (e.g. a new account), search `index.html` for `FORMSPREE_ENDPOINT` and swap in the new endpoint URL. To change the destination email used by the mailto fallback specifically, search for `SUGGESTIONS_EMAIL`.

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

## How scoring works (implementation note)

Mixed sets present questions in a shuffled order (`state.order`, built by `shuffledOrder()`), while `state.answers` is indexed by the *display position* a question was shown at, not by its position in the underlying pool array. Scoring and the results/review screen must pair `state.answers[i]` with `pool[state.order[i]]` (the question actually shown at step `i`) — comparing against `pool[i]` directly would silently mis-score shuffled sets. `finishQuiz()`, `renderResults()`, and `downloadResultsDoc()` all use the `state.order`-aware pairing; keep that pattern if you touch this code.
