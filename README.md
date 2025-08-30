# BatAI Deck — Velaris (PPTX Generator)

Generate polished PowerPoint decks from text/Markdown using your favorite LLM provider. BatAI Deck ships with a vibrant Velaris theme (glass surfaces, starfield, amethyst/teal accents) and a streamlined UI.

This project consists of a single-page frontend (`index.html`) and a FastAPI backend (`app.py`) that builds `.pptx` files with `python-pptx`.

## Highlights

- Multi‑provider LLM planning (JSON-only)
  - OpenAI, AI Pipe (OpenRouter-compatible), Google Gemini, Anthropic (Claude)
- New UI structure
  - Top bar with “Provider & Model” button (modal), Theme toggle, Settings
  - Two-column Command Deck: Compose (left) | Actions & Preview (right)
  - Full-width bottom History strip (horizontal scroll)
  - Floating quick-action dock (desktop) + sticky action bar (mobile)
- Velaris theme
  - Glassmorphism surfaces, starfield background, Cinzel headings + Inter body
  - Dark/Dusk/Light theme modes; respects `prefers-reduced-motion`
- Templates and safe image reuse
  - Upload `.pptx/.potx` template, optionally re-use images safely (no text overlap)
- Strong slide constraints (via LLM prompt)
  - Titles ≤ 80 chars; 3–6 bullets per slide, each ≤ 120 chars
  - Min 10 slides by default; target 1–40 supported
- Local history (metadata only)
  - Export/clear in one click; no keys are stored
- Rebrand + migration
  - Project renamed from “GyaanSetu” to “BatAI”
  - LocalStorage automatically migrates `gyaan_*` keys to `batai_*`

## Requirements

- Python 3.10+
- pip

Python dependencies (installed via `requirements.txt`):
- fastapi, uvicorn
- python-pptx
- requests
- Optional SDKs (install are handled by `requirements.txt`):
  - openai
  - anthropic
  - google-genai

## Quickstart (Local)

1) Create a virtualenv (optional) and install dependencies:
```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

2) Run the FastAPI server:
```bash
uvicorn app:app --reload --port 8000
```

3) Open the app in your browser:
```
http://localhost:8000
```
Do not open `index.html` directly with `file://` — the UI posts to `/generate` on the same origin.

## Using the App

1) Click the sliders icon in the top bar to open “Provider & Model”
   - Choose Provider (OpenAI, AI Pipe, Gemini, or Anthropic)
   - Paste your API key (clipboard button available)
   - Pick a model (the list updates per provider)

2) Compose
   - Paste or type your Text/Markdown (≥ 10 chars)
   - Provide optional one-line guidance (e.g., “executive summary”, “investor pitch”)
   - Set Number of slides (1–40; default is 10)
   - Optional: attach a `.pptx/.potx` template and enable “Reuse images” for safe, non-overlapping placement

3) Generate & Download
   - Click “Generate .pptx”
   - On success, “Download last” is enabled (also available in the output preview card)
   - A history entry appears at the bottom (metadata only)
   - Export history to JSON or clear it any time

4) Theme & Settings
   - Theme toggle cycles Dark → Dusk → Light
   - Settings: adjust “Max history items” (persisted in localStorage)

Keyboard shortcut: Ctrl/Cmd + G to generate when enabled.

## Providers & Models

- OpenAI: `gpt-4o-mini`, `gpt-4o`, `gpt-4.1`, `gpt-4.1-nano`, `gpt-4o-realtime-preview`
- AI Pipe (OpenRouter-compatible): `gpt-4o-mini`, `gpt-4o`, `gpt-4.1-nano`
- Gemini: `gemini-1.5-flash`, `gemini-2.1`, `gemini-2.5-flash`, `gemini-2.5-pro`
- Anthropic: `claude-3-5-sonnet`, `claude-3-5-sonnet-latest`, `claude-3`, `claude-2.1`

Defaults if not specified (per backend in `app.py`):
- OpenAI/AI Pipe: `gpt-4o-mini`
- Anthropic: `claude-3-5-sonnet-latest`
- Gemini: `gemini-2.5-flash`

## Slide Limits & Text Limits

- Text input clamped to 60,000 characters
- Slides:
  - If you specify a target, result is enforced to exactly that count (1–40)
  - Else, min 10 slides are ensured; dense slides may be split; capped at 40

## Templates & Safe Image Reuse

- Upload `.pptx`/`.potx` up to 30 MB
- If “Reuse images” is checked:
  - PICTURE shapes from each template slide are copied first
  - Images are auto-repositioned/scaled to avoid overlapping text zones
  - Text is layered on top to remain readable

## History & LocalStorage

- History stores only metadata (timestamp, short title preview, provider/model, slide count)
- Keys (post-rebrand):
  - `batai_deck_history_v1`
  - `batai_deck_state_v1` (stores a short API key prefix for convenience and max history size)
- Migration:
  - On first load, values from `gyaan_deck_history_v1` / `gyaan_deck_state_v1` are migrated automatically and the old keys are removed
- Exported filename: `batai_deck_history.json`

## Troubleshooting

- 401 Unauthorized / invalid key:
  - Ensure you picked the correct provider for your key
  - Confirm model access for your account
- 429 Rate limit / quota:
  - Reduce requests or use a different model/provider with available quota
- “Model not allowed / unsupported”:
  - Pick a model your key is allowed to call
- 500 LLM error:
  - Try a simpler prompt, different provider/model, or verify SDK versions
- 500 PowerPoint build error:
  - Try without a template, then with a smaller valid `.pptx`
- Clipboard access denied:
  - Manually paste your key; adjust browser permissions for clipboard API

## Project Structure

```
.
├─ app.py           # FastAPI server: / (serves index.html), /generate, /favicon.ico
├─ index.html       # Single-file frontend (Velaris theme + starfield)
├─ requirements.txt # Python dependencies
├─ vercel.json      # (Optional) deployment config
├─ LICENSE
└─ README.md
```

## Security & Privacy

- No API keys are stored server-side
- The UI stores only a short prefix of your key locally (for convenience) and can be cleared
- Generated PPTX is streamed back to the browser and not persisted on the server

## License

See [LICENSE](./LICENSE).

## Changelog (UI/Branding)

- 2025-08-30
  - Rebrand UI from “GyaanSetu Deck” to “BatAI Deck”
  - Move Provider & Model into a top-bar modal; keep Compose/Actions as main
  - Introduce bottom History strip, floating dock, and mobile sticky actions
  - Preserve Velaris theme with Dark/Dusk/Light modes
