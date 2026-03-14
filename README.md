<p align="center">
  <img src="assets/banner.png" alt="Fuzzie Banner" width="400">
</p>

<p align="center"><em>Talk to it. It breaks Wi-Fi.</em></p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3.7+-blue">
  <img src="https://img.shields.io/badge/License-Unlicense-blue">
  <img src="https://img.shields.io/badge/Status-Hackathon%20WIP-orange">
</p>

---

Fuzzie is a Wi-Fi protocol fuzzer you control through conversation. Instead of memorizing CLI flags, you describe what you want to test — Fuzzie figures out the parameters, runs the fuzzer, and hands you a report when something breaks.

Built for **802.11**, but the architecture is intentionally protocol-agnostic.

---

## The Idea

Traditional protocol fuzzers are powerful but annoying to use. You need to know the exact frame types, field offsets, mutation strategies — before you've even started testing. We wanted something where you could just say *"fuzz beacon frames on this router"* and have it work.

<svg width="100%" viewBox="0 0 680 400" xmlns="http://www.w3.org/2000/svg">
  <defs>
    <marker id="arrow" viewBox="0 0 10 10" refX="8" refY="5" markerWidth="6" markerHeight="6" orient="auto-start-reverse">
      <path d="M2 1L8 5L2 9" fill="none" stroke="#888" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
    </marker>
  </defs>

  <!-- ================================================================
    BUTTERFLY DIAGRAM — HOW TO EDIT
    
    Each layer is a <g> block with a <rect>, title <text>, subtitle <text>
    
    To adjust WIDTH of a layer:
      - Change rect x and width
      - left edge  = x
      - right edge = x + width
      - Update the funnel <line> x1/x2 values below it to match those edges
    
    To add a layer:
      - Copy a <g> block + its funnel lines, paste below, bump y values by ~100
      - Increase viewBox height to fit (currently 400)
    
    To change colors:
      - rect fill / stroke
      - text fill (title vs subtitle can differ)
    ================================================================ -->


  <!-- ── LAYER 1 — top, widest ───────────────────────────────────────
       left edge: 140    right edge: 540    (width 400)              -->
  <g>
    <rect x="140" y="20" width="400" height="56" rx="10"
          fill="#EEEDFE" stroke="#7F77DD" stroke-width="0.8"/>
    <text x="340" y="42" text-anchor="middle" dominant-baseline="central"
          font-family="sans-serif" font-size="14" font-weight="600" fill="#3C3489">
      You
    </text>
    <text x="340" y="62" text-anchor="middle" dominant-baseline="central"
          font-family="sans-serif" font-size="12" fill="#534AB7">
      chat · voice · CLI
    </text>
  </g>

  <!-- funnel: left 220→265, right 460→415, center arrow down -->
  <line x1="220" y1="76" x2="265" y2="140" stroke="#ccc" stroke-width="0.8"/>
  <line x1="460" y1="76" x2="415" y2="140" stroke="#ccc" stroke-width="0.8"/>
  <line x1="340" y1="76" x2="340" y2="136" stroke="#999" stroke-width="1.5" marker-end="url(#arrow)"/>


  <!-- ── LAYER 2 — narrows ───────────────────────────────────────────
       left edge: 220    right edge: 460    (width 240)              -->
  <g>
    <rect x="220" y="140" width="240" height="56" rx="10"
          fill="#E1F5EE" stroke="#1D9E75" stroke-width="0.8"/>
    <text x="340" y="160" text-anchor="middle" dominant-baseline="central"
          font-family="sans-serif" font-size="14" font-weight="600" fill="#085041">
      Orchestration Agent
    </text>
    <text x="340" y="180" text-anchor="middle" dominant-baseline="central"
          font-family="sans-serif" font-size="12" fill="#0F6E56">
      routes intent · manages session
    </text>
  </g>

  <!-- funnel: left 270→300, right 410→380, center arrow down -->
  <line x1="270" y1="196" x2="300" y2="230" stroke="#ccc" stroke-width="0.8"/>
  <line x1="410" y1="196" x2="380" y2="230" stroke="#ccc" stroke-width="0.8"/>
  <line x1="340" y1="196" x2="340" y2="226" stroke="#999" stroke-width="1.5" marker-end="url(#arrow)"/>


  <!-- ── LAYER 3 — waist, narrowest ────────────────────────────────
       left edge: 270    right edge: 410    (width 140)              -->
  <g>
    <rect x="270" y="230" width="140" height="56" rx="10"
          fill="#FAEEDA" stroke="#BA7517" stroke-width="0.8"/>
    <text x="340" y="250" text-anchor="middle" dominant-baseline="central"
          font-family="sans-serif" font-size="14" font-weight="600" fill="#633806">
      Fuzzer Agent
    </text>
    <text x="340" y="270" text-anchor="middle" dominant-baseline="central"
          font-family="sans-serif" font-size="12" fill="#854F0B">
      intent → config → run
    </text>
  </g>

  <!-- expand: left 300→265, right 380→415, center arrow down -->
  <line x1="300" y1="286" x2="265" y2="320" stroke="#ccc" stroke-width="0.8"/>
  <line x1="380" y1="286" x2="415" y2="320" stroke="#ccc" stroke-width="0.8"/>
  <line x1="340" y1="286" x2="340" y2="316" stroke="#999" stroke-width="1.5" marker-end="url(#arrow)"/>


  <!-- ── LAYER 4 — bottom, widest again ───────────────────────────
       left edge: 140    right edge: 540    (width 400)              -->
  <g>
    <rect x="140" y="320" width="400" height="56" rx="10"
          fill="#FAECE7" stroke="#D85A30" stroke-width="0.8"/>
    <text x="340" y="340" text-anchor="middle" dominant-baseline="central"
          font-family="sans-serif" font-size="14" font-weight="600" fill="#4A1B0C">
      Reporting Agent
    </text>
    <text x="340" y="360" text-anchor="middle" dominant-baseline="central"
          font-family="sans-serif" font-size="12" fill="#993C1D">
      crash logs → LLM analysis → PDF report
    </text>
  </g>

</svg>

Each layer is independent — you can swap the chat interface, the fuzzer engine, or the LLM without touching the rest.

---

## What's Working

- **Conversational interface** via Telegram  — send a message, get fuzzing
- **LLM-driven config generation** — describe your target, the agent generates fuzzer parameters
- **Live status updates** — progress streams back to your chat as it runs
- **Crash detection + PDF reports** — when something breaks, you get a structured report with CVE correlation

---

## What's WIP

> *Hackathon build — these sections aren't done yet*

- [ ] **Voice interface** — architecture supports it, adapter not built
- [ ] **CVE correlation** — report template exists, actual CVE lookup is stubbed
- [ ] **Horizontal scaling** — designed for it, not tested with multiple agents
- [ ] **Non-802.11 protocols** — Bluetooth/Zigbee support is on the roadmap

---

## File Structure

| File | What it does |
|------|-------------|
| `agent.py` | Entry point — wires everything together |
| `telegram_adapter.py` | Chat interface (swap this for other platforms) |
| `gemini_adapter.py` | LLM client — returns structured JSON decisions |
| `fuzz.py` | The actual fuzzer CLI |
| `report_generator.py` | Logs → LLM → HTML → PDF |
| `system_prompt.py` | Prompt for fuzzing decisions |
| `report_prompt.py` | Prompt for crash report generation |

---

## Quick Start

**Prerequisites:** Python 3.7+, a wireless adapter that supports monitor mode, an LLM API key (Gemini or OpenAI)

```bash
git clone https://github.com/yourusername/fuzzie.git
cd fuzzie
pip install -r requirements.txt
cp .env.example .env
# add your API keys to .env
sudo python3 agent.py
```

Then in Telegram:
1. `/start`
2. Pick **"Let's start Fuzzing!"**
3. Describe what you want to test — e.g. *"fuzz beacon frames on a TP-Link router"*
4. Confirm the config it generates
5. Watch it run

---

## Learning Mode

Not just a fuzzer — you can also ask it questions about 802.11 frames, protocol internals, or Wi-Fi security concepts. The orchestration agent switches modes automatically based on what you're asking.

---

## Extending It

The three-layer split makes swapping things out straightforward:

- **Different chat platform?** Replace `telegram_adapter.py`
- **Different protocol?** Replace `fuzz.py` with a Bluetooth/Zigbee fuzzer
- **Different LLM?** Replace `gemini_adapter.py`

---

## License

[Unlicense](https://unlicense.org) — public domain, do whatever you want with it.
