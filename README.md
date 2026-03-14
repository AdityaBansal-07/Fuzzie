<p align="center">
  <img src="assets/banner.png" alt="Fuzzie Banner" width="400">
</p>

<h1 align="center">Fuzzie</h1>
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

So we built a three-layer agent system:

```
You (chat/voice)
    │
    ▼
Orchestration Agent       ← routes your intent, manages session
    │
    ▼
Fuzzer Agent (LLM + CLI)  ← translates intent → fuzzer config → runs it
    │
    ▼
Reporting Agent           ← parses crash logs → generates PDF report
```

Each layer is independent — you can swap the chat interface, the fuzzer engine, or the LLM without touching the rest.

---

## What's Working

- **Conversational interface** via Telegram (demo) — send a message, get fuzzing
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
