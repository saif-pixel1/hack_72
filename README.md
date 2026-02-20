# Nature Meditation Guide

> Enter a natural location and receive a streaming, AI-generated one-minute guided breathing meditation — written for that exact place.

---

## Overview

Nature Meditation Guide is a single-file browser app that uses the Claude API to generate immersive, location-specific breathing meditation scripts in real time. Type any natural setting — a pine forest, an ocean cliff, a desert at dawn — and a serene 160–180 word meditation script streams back word by word, paired with a living breathing orb animation to guide your inhale and exhale rhythm.

No frameworks. No build step. One HTML file, opens in any browser.

---

## Features

- **Location input** — Free-text field accepts any natural setting, real or imagined
- **Six suggestion chips** — One-click presets: Pine Forest, Ocean Shore, Alpine Meadow, Bamboo Grove, Desert at Dawn, Rainforest
- **Streaming output** — Script arrives token-by-token via the Anthropic streaming API with a live cursor
- **Breathing orb** — Pulsing three-ring orb animates on an 8-second breath cycle throughout the session
- **Breath phrase cycle** — Rotates through *inhale slowly → hold gently → exhale fully → rest here* every 2 seconds
- **Floating leaf particles** — 18 ambient leaf elements drift across the background
- **Copy Script** — One-click clipboard copy of the completed meditation
- **New Session** — Regenerate a fresh script for the same or a new location
- **Enter key support** — Submit without reaching for the mouse
- **Fully responsive** — Works from 375px wide upward

---

## Usage

1. Open `nature-meditation.html` in any modern browser
2. Type a natural location into the input field, or click a suggestion chip
3. Press **Begin** or hit Enter
4. Watch the script stream in — breathe with the orb while you read
5. Use **Copy Script** to save the text, or **New Session** to start again

No installation required. See [API Key](#api-key) below for key handling.

---

## File Structure

```
nature-meditation/
├── nature-meditation.html    # Complete application (single file)
├── README.md                 # This file
└── REQUIREMENTS.md           # Functional & technical requirements
```

---

## API Key

The app calls the Anthropic Messages API directly from the browser. For production deployment, **never expose your API key in frontend code**. Route requests through a backend proxy instead:

```
Browser → POST /api/meditate { location } → Your Server → Anthropic API → SSE stream
```

Minimal Node.js/Express proxy:

```js
app.post('/api/meditate', async (req, res) => {
  const { location } = req.body;
  const upstream = await fetch('https://api.anthropic.com/v1/messages', {
    method: 'POST',
    headers: {
      'Content-Type': 'application/json',
      'x-api-key': process.env.ANTHROPIC_API_KEY,
      'anthropic-version': '2023-06-01'
    },
    body: JSON.stringify({
      model: 'claude-sonnet-4-20250514',
      max_tokens: 1024,
      stream: true,
      messages: [{ role: 'user', content: buildPrompt(location) }]
    })
  });
  upstream.body.pipe(res);
});
```

---

## Prompt Design

The generation prompt instructs Claude to:

- Write **160–180 words** — calibrated for ~1 minute of slow, mindful reading aloud
- Open with sensory grounding specific to the entered location (sights, sounds, scents)
- Weave **3 breath cycles** naturally into the environment's imagery
- Use soft, present-tense, immersive language with no clinical or instructional tone
- Close with stillness and a quiet moment of gratitude
- Output the script only — no preamble, headers, or meta-commentary

---

## Tech Stack

- Vanilla HTML5 / CSS3 / JavaScript (ES2020) — zero dependencies
- Google Fonts: Cormorant Garamond (script display), Jost (UI labels)
- Anthropic Messages API with `stream: true` and `ReadableStream` parsing
- CSS keyframe animations for orb breathe, leaf fall, and cursor blink
- `navigator.clipboard` for copy functionality

---

## Browser Support

| Browser | Minimum Version |
|---|---|
| Chrome | 90+ |
| Firefox | 88+ |
| Safari | 14+ |
| Edge | 90+ |

---

## License

MIT — free to use, adapt, and share.
