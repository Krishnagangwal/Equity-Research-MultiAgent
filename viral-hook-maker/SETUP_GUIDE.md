# Viral Hook Maker v2: Setup Guide

An n8n workflow that:

1. scrapes the latest TikToks from a list of creators (Apify),
2. scores each video by **views per day relative to follower count** and keeps the top 30%,
3. transcribes each video (OpenAI Whisper) and keeps the **first 7 seconds**, which is the hook,
4. teaches GPT the winning hook patterns and writes **10 new hooks for your topic**,
5. saves everything to Google Sheets.

File to import: [`VIRAL_HOOK_MAKER_v2.json`](./VIRAL_HOOK_MAKER_v2.json)
Google Sheet (already created and linked in both Sheets nodes, with tabs `data` and `final result`):
https://docs.google.com/spreadsheets/d/1LxnAdLJVjYSc0UosFGZt60JivLWW2kUmjE_gtGdaFkk/edit

---

## What you need (about 15 minutes, one time)

| Account | What for | Where to get it |
|---|---|---|
| **n8n** | Runs the workflow | [n8n.io](https://n8n.io) Cloud (easiest) or self-hosted |
| **Apify** | Scrapes TikTok | [console.apify.com](https://console.apify.com) → Settings → **API & Integrations** → copy your API token |
| **OpenAI** | Transcription + hook writing | [platform.openai.com/api-keys](https://platform.openai.com/api-keys) (needs a few dollars of credit) |
| **Google** | Sheets output | The Google account that owns the sheet above |

## Step 1: Import the workflow
n8n → **Workflows** → **⋯ / Add workflow** → **Import from file** → choose `VIRAL_HOOK_MAKER_v2.json`.

## Step 2: Add the 3 credentials
Open each node below, click the **Credential** dropdown, choose **Create new**:

| Node(s) | Credential type | What to enter |
|---|---|---|
| **Scrape TikTok (Apify)** | **Header Auth** | Name: `Authorization`  ·  Value: `Bearer YOUR_APIFY_TOKEN` (the word *Bearer*, a space, then the token) |
| **Transcribe (Whisper)** and **Write Hooks (OpenAI)** | **OpenAI API** | Paste your OpenAI API key. Use the same credential for both nodes. |
| **Save Videos to Sheet** and **Save Hooks to Sheet** | **Google Sheets OAuth2 API** | Click **Sign in with Google**, using the account that owns the sheet |

## Step 3: Check the Config node
Open **Config**. It's the only node you ever edit:
- `topic`: what your next video is about (the AI writes hooks for this)
- The sheet above is already linked in both Google Sheets nodes
- `profiles`: the creators to learn from (your original 34 are already there)
- `videosPerProfile`, `maxAgeDays`, `topPercent`, `maxVideosToTranscribe`, `hookCount`, `model`

## Step 4: Run it
Click **Execute workflow**. A run takes about 3–8 minutes; the Apify scrape is the slow part.
Your hooks show up in the **`final result`** tab. The `data` tab shows every winning video and its transcribed hook.

---

## What changed from the original file

| Original problem | Fix |
|---|---|
| Retry `If` node had its outputs backwards, so failures carried on and successes looped forever | Removed. TikWM calls are spaced 1.5 s apart, retried automatically, and failures are skipped |
| Transcription step dropped author/views/etc., so the sheet got empty columns | Every video's details are carried through to the end |
| Transcribed TikTok's *music* track, which is often a song, not the creator's voice | Transcribes the actual video (falls back to music only if the file is too big) |
| Needed ffmpeg + disk writes + Execute Command (blocked on n8n Cloud and new n8n versions) | Not needed: Whisper returns timestamps and the first 7 s are cut from the text |
| Song lyrics polluted the training data | Whisper's "no speech" score marks each hook `spokenHook: yes/no`; music-only videos are learned from caption only |
| Wrote to Sheets, then read the same data back | Data flows straight to the AI; Sheets is just the record |
| AI output split on blank lines (broke if the model formatted differently) | Model returns strict JSON: hook, on-screen text, pattern, which example inspired it, why it works |
| Sheet links and settings scattered across 5 nodes | One **Config** node |
| Sheet had to be cleared before each run | Every row gets a `runId`, so runs never mix |

## Approximate cost per run (defaults: 34 profiles × 2 videos, top 25 max)
- **Apify:** ~68 results. Clockworks TikTok Scraper charges per result; check the current price on its Apify page (usually a few cents per run, and the free tier covers a lot).
- **OpenAI Whisper:** about $0.006 per minute of video, so roughly $0.05–$0.25 per run.
- **gpt-4.1-mini:** under $0.01.

## Troubleshooting

| Error | Fix |
|---|---|
| `Apify returned no usable videos` | Check the Apify credential value starts with `Bearer `; check profile spellings (no `@`); raise `maxAgeDays` |
| Apify node times out | Too many profiles for one synchronous run. Lower `videosPerProfile` or split the `profiles` list |
| `TikWM could not return media…` | TikWM (a free service) is rate-limiting. Wait a few minutes and rerun |
| `No transcripts came back from OpenAI` | Check the OpenAI key and that the account has billing credit |
| Google Sheets "sheet not found" | Tab names must be exactly `data` and `final result` |
| `The AI did not return valid JSON` | Rerun; if it keeps happening, set `model` to `gpt-4.1` in Config |

> Heads-up: a few usernames in the original list look like typos (`Minolee.mp4`, `Baxate_carter`, `Rpn`). Profiles Apify can't find are skipped automatically, but you may want to fix them.
