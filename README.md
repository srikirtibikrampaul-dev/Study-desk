# Study Desk

**AI-Powered Student Workspace** — turns a lecture into revision notes and a
practice quiz, in one flow, end to end.

## What it does

Upload a lecture PDF, Word doc, or plain text (or just paste text — handy
for slide content). Study Desk reads it and generates:

- **Condensed revision notes**, organized into short, scannable sections
- **A 5-question practice quiz** built from the same material, with
  instant grading and explanations

Both can be exported as a Markdown file or copied as plain text.

### Bonus features included

- Multiple input formats: PDF, `.docx`, `.txt`/`.md`, or pasted text
- Export to a shareable `.md` file
- Light personalization: optional subject/course fields tailor the notes
  and quiz's examples and emphasis

## How it's demoed for this submission

The interface calls Anthropic's Claude API to generate the study pack.
Rather than exposing an API key in the browser (a real security risk for
a publicly hosted static site), the code is built to call a small
serverless function — `api/generate.js` (Vercel) or
`netlify/functions/generate.js` (Netlify) — which holds the API key
server-side.

**For this hackathon, the live demo runs inside Claude's own artifact
preview**, which provides API access directly with no key management
needed on our end. The repo here is the full source, including the
serverless proxy setup, ready to deploy to Vercel or Netlify by anyone
who wants to self-host it with their own Anthropic API key — see
`DEPLOY.md` for that walkthrough.

## How it works

1. The browser extracts text from the uploaded file (PDF.js for PDFs,
   Mammoth.js for `.docx`; plain text files are read directly).
2. That text, plus any subject/course context, is sent to Claude with a
   prompt constraining the output to a strict JSON shape: a fixed number
   of note sections and exactly 5 quiz questions.
3. The response is validated (correct shape, valid answer indices) before
   being rendered, so a malformed or truncated response fails with a
   clear retry message instead of showing a broken quiz.
4. Everything renders in the browser — no data is stored anywhere.

## Project structure

```
index.html                     the site (single file: HTML, CSS, JS)
api/generate.js                serverless function for Vercel
netlify/functions/generate.js  serverless function for Netlify
netlify.toml                   routes /api/* to the Netlify function
package.json                   specifies Node version for the functions
DEPLOY.md                      step-by-step deployment guide
```

## Deploying it yourself

See `DEPLOY.md` for the full walkthrough: get an Anthropic API key,
connect this repo to Vercel or Netlify, set `ANTHROPIC_API_KEY` as an
environment variable, and deploy. No code changes are needed — the app
already looks for `/api/generate` first and only falls back to Claude's
built-in artifact access if that route isn't found.

## Tech

Plain HTML/CSS/JS on the front end (no build step, no framework),
PDF.js and Mammoth.js for in-browser file parsing, and a serverless
function for the API call. Built with Claude's help.
