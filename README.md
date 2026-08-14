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

## Homepage design

- **Category cards** sit in a fixed 2×2 grid, each with a small topic icon (book / pen / message bubble / check), a colored accent bar/icon background matching its domain, topic pills that highlight on hover with a reveal arrow, a progress bar showing sets attempted, and an explicit **"Explore Sets →"** button in the bottom-right corner as the click target (the whole card is still clickable too).
- **Set rows** (both the 5 mixed-practice sets and each domain's category sets) show a status pill — a gray "○ Not Started" badge, or a green "✓ Completed" badge paired with a small color-coded ring showing the best score (green ≥80%, amber 50–79%, red below 50%) — plus a clearly differentiated action button: solid blue **Start** for a fresh set, outline **Retry** once it's been completed. This logic lives in the shared helpers `renderSetStatus()` and `renderSetButton()`, used by both `renderHome()` and `renderSetList()` so category sets and mixed sets stay visually consistent.
- **Cards throughout the site** (domain cards, set rows, the quiz card, results, the suggestions form, review items, and the Privacy/Terms pages) share a subtle drop shadow, and domain cards additionally lift slightly on hover, for a bit more depth than the flat original design.

## Feedback / suggestions

The top bar has a **"Your Suggestions Please"** button that opens a small in-page form (name/email optional, category, message).

On submit, the form first tries **[Formspree](https://formspree.io)** — a free form-backend service. It posts directly to a Formspree endpoint (`FORMSPREE_ENDPOINT` in `index.html`, currently `https://formspree.io/f/xbgrkqgo`), and Formspree emails the submission to whoever owns that form automatically, no dashboard setup required beyond creating the form once. (We tried Netlify Forms first, but Netlify's email notifications turned out to require a paid plan — Formspree's free tier includes real email notifications, so we switched.) If the Formspree request fails for any reason (offline, endpoint unreachable, page opened as a local file), the form falls back to opening a `mailto:` draft addressed to `akatlantajan12@gmail.com` instead.

To change where submissions go: log into [formspree.io](https://formspree.io), open the form, and update its notification email in Settings — no code change needed. To point the site at a *different* Formspree form entirely (e.g. a new account), search `index.html` for `FORMSPREE_ENDPOINT` and swap in the new endpoint URL. To change the destination email used by the mailto fallback specifically, search for `SUGGESTIONS_EMAIL`.

## Footer: visitor counter, Privacy Policy, Terms of Use, copyright

The footer at the bottom of every screen shows four things:

- **"X people have viewed this site"** — a live visitor count. This uses a free, no-signup hit-counter API ([abacus.jasoncameron.dev](https://abacus.jasoncameron.dev)) that increments one shared, anonymous number every time the page loads. The count is fetched once on page load (see `initFooter()` near the bottom of the `<script>` block, search for `abacus.jasoncameron.dev`) and written into `#footerStats`. If the API is ever unreachable, this line is simply left blank — it fails silently and never breaks the page. The counter key is `satenglish-online-akatlanta/site-visits`; if you ever need to reset or swap it, change that path (a new path starts a fresh count at 0/1).
- **Privacy Policy** and **Terms of Use** — two standalone info pages (`renderPrivacy()` / `renderTerms()`, opened via `openPrivacy()` / `openTerms()`), built the same way as the Suggestions screen. The Terms page states plainly that this is a free, unofficial resource not affiliated with the College Board and that using it doesn't guarantee any particular SAT score or outcome. Edit the text directly inside those two functions to change the wording.
- **Copyright line** — `© <current year> FREE SAT English Preparation. All rights reserved.` The year is computed automatically (`new Date().getFullYear()`), so it never needs manual updating.

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

## Content audit vs. the current digital SAT (Aug 2026)

All 300 questions were checked against College Board's current official Reading & Writing framework (domains, subskills, ~25-150 word passage length, and the exact stem phrasing used for each question type, e.g. Rhetorical Synthesis's "bulleted notes + stated goal" format). The domain/subskill structure and stem conventions already matched closely. This pass found and fixed:

- **A real rendering bug on every Cross-Text Connections question (25 total, 5 per set):** each `passage.a`/`passage.b` string had its own embedded "Text 1: " / "Text 2: " prefix, which duplicated the label the site already renders automatically. Fixed by stripping the embedded prefixes from the data.
- **A stray broken `</br>` tag** in one Inferences question (Full Mixed Practice Set 4) that would have rendered as visible garbage text.
- **One genuinely inconsistent explanation** (Set 2, thermohaline circulation question) that referenced the wrong choice numbers — rewritten for accuracy.
- **One ambiguous Boundaries question** (scholarship committee) where two of the four choices were both grammatically defensible — replaced the weaker distractor so only one choice is correct.
- **One factual correction**: a Rhetorical Synthesis note about the spread of papermaking said the Battle of Talas (751 CE) happened "near Samarkand" — it was actually fought near the Talas River; the captured papermakers were later brought to Samarkand. Corrected the note and its matching answer choice.
- **Five narrative-fiction passages rewritten as informational/expository prose** (one in Set 1, four in Set 4 — arts/literature/language), since the current digital SAT's Reading & Writing passages are informational/nonfiction, not short fiction, even when the topic is art or music. Rewrote each to test the same skill (Central Ideas & Details / Inferences) with the same difficulty, just with real-world informational content instead of a fictional scene.

**Not changed, but worth knowing:** the site currently has no *quantitative* Command of Evidence questions (the graph/table variant the real test also uses) — the site's passage renderer only supports text and two-text passages, not a chart/table. All 25 Command of Evidence questions here are the textual (quotation/finding) variant, which is valid but only half the picture. Also, each 60-question set is split evenly across the four domains (15/15/15/15, 25% each) rather than the official ~28%/26%/26%/20% weighting — close, but not an exact match. Both are optional future improvements, not defects in what's there today.
