# UniVerse

UniVerse is a static site that helps students navigate Rajasthan DTE (Directorate
of Technical Education) engineering and polytechnic admissions. It has a landing
page with a rank-based college predictor, and a chat page for asking admissions
questions in plain language.

**UniVerse is an independent project and is not affiliated with, endorsed by, or
in any way officially connected to the Government of Rajasthan or its DTE.**

## Stack

Plain static HTML/CSS/JS — no build step, no framework, no bundler. [Vitest](https://vitest.dev)
is used for the test suite.

## Structure

```
index.html          Landing page + rank-based college predictor
chat.html            Chat page (Zapier embed by default, local fallback available)
privacy.html          Privacy policy
assets/css/           Stylesheets
assets/js/            Predictor, chat provider/UI, sources modal, generated data
assets/img/           Logo/images
tools/build-data.py   Regenerates assets/js/data.js from the source workbook
tests/                Vitest test suite
```

## Running locally

Serve the folder with any static file server, e.g.:

```
npx serve .
```

**Important:** `chat.html` loads the Zapier chatbot widget from a CDN via a
`type="module"` script tag. Browsers block ES module scripts on the `file://`
protocol, so `chat.html` must be served over http(s) — opening it directly by
double-clicking the file will not work. `index.html` and `privacy.html` have no
such restriction and work fine from `file://` too.

## Chat

By default `chat.html` embeds the Zapier chatbot. A built-in fallback chat that
answers from the bundled dataset (no external service) is available at:

```
chat.html?chat=local
```

### Optional: OpenCode fallback provider

The local chat mode can optionally use an OpenCode API key instead of its
built-in dataset matching. To enable it, copy `assets/js/config.example.js` to
`assets/js/config.local.js` and fill in a key:

```
window.UNIVERSE_CONFIG = { OPENCODE_API_KEY: 'your-key-here' };
```

`config.local.js` is gitignored and never committed. **Any key placed in it is
visible in plain text to every visitor of a deployed site** (it ships in a
browser-loaded JS file), so this is only suitable for a private demo. For a
public launch, put the key behind a server-side proxy instead.

## Tests

```
npm install
npm test
```

This runs the Vitest suite (72 tests) covering the predictor logic, the chat
provider, the generated data, and page content.

## Data

`assets/js/data.js` (colleges, cutoffs, FAQs) is generated from
`2026.08-College-Database-final.xlsx` by:

```
python tools/build-data.py
```

Re-run this any time the workbook changes.

**Note on accuracy:** most cutoff figures in the dataset are **estimated**, not
official. Only around 30 real cutoff rows (covering roughly 14 colleges) come
from verified sources — the rest are modeled estimates used to give directional
guidance. Treat all predictor output and cutoff numbers as guidance, not as
official DTE/REAP cutoffs, and always confirm against the official DTE/REAP
publications before making decisions.
