# FOCUS — Ambient Financial Awareness Device

> *Financial awareness without the anxiety of opening an app.*

---

## What It Is

FOCUS is a physical + digital product that helps college students and young adults stay financially aware passively. A glowing cube sits on your desk. Each of the 6 faces represents one bank account. The face glows **green**, **yellow**, or **red** based on Claude AI's daily analysis of your spending health.

No app required. No dashboard to check. Awareness happens because the object exists in your space.

---

## The Problem It Solves

Most people don't fail financially because they lack information, they fail because checking finances feels stressful, so they avoid it entirely. Traditional budgeting apps require sustained attention and emotional comfort with numbers. FOCUS removes the decision to check. Awareness becomes ambient.

---

## System Architecture

```
Plaid API (mock)
      │
      │  REST
      ▼
Flask Backend ──── Claude AI
  (Device 1)           │
      │           scores + nudges
      │  MQTT           │
      ▼                 ▼
MQTT Broker ──► Web Dashboard / Raspberry Pi
                   (Device 2)
                        │
                   user dismisses green
                        │  MQTT back
                        ▼
                 Flask updates state
```

---

## How Information Moves

1. **Mock Plaid JSON** → Flask parses 6 account objects (balance, transactions, bills due)
2. **Flask → Claude API** → Claude returns `{ score: 0-100, nudge: "plain language string" }` per account
3. **Score mapper** → score ≥ 70 = green, 40–69 = yellow, < 40 = red
4. **Flask → MQTT broker** → publishes JSON payload to topic `focus/{account_id}`
5. **Dashboard subscribes** → each panel updates glow color + nudge text in real time
6. **User taps dismiss** → POST to `/dismiss` → Flask publishes `dismissed: true` back to broker
7. **Dashboard receives** → dismissed panel fades out

---

## Running the Prototype

### Prerequisites
```
pip install flask anthropic paho-mqtt
```

### Terminal 1 — Device 1 (Backend)
```bash
cd focus-backend
python app.py
# Flask running on localhost:5000
```

### Terminal 2 — Device 2 (Dashboard)
```bash
open index.html
# OR serve with: python -m http.server 8080
```

### Trigger Analysis
```
Open browser → http://localhost:5000/analyze
Watch Terminal 1 print each account analysis
Watch dashboard panels light up in real time
Click dismiss on any green panel
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Bank Data | Plaid API (mocked JSON for demo) |
| Backend | Python / Flask |
| AI Enrichment | Anthropic Claude API |
| Communication | MQTT / HiveMQ Cloud (free tier) |
| Frontend | HTML / CSS / JavaScript / MQTT.js |
| Physical Device | Raspberry Pi + RGB LED + Accelerometer |

---

## Project Structure

```
focus/
├── app.py              # Flask backend — Device 1
├── accounts.json       # Mock Plaid data
├── index.html          # Web dashboard — Device 2
├── README.md           # This file
└── diagrams/
    └── architecture.html   # System architecture diagram
```

---

## Rubric Mapping

| Requirement | Implementation |
|---|---|
| Device Connectivity | Flask (Device 1) ↔ Dashboard (Device 2) via MQTT |
| ML/LLM Enrichment | Claude API scores each account + generates nudge |
| Visual Presentation | Live-updating dashboard with glow states and nudge text |
| User Need | Reduces financial avoidance through ambient awareness |
| Design Diagrams | Architecture diagram + storyboard in /diagrams |

---

*Built for DTI Midterm — Duke University MEng Program*
