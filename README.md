# VisionAid

**An AI-powered outdoor navigation assistant for visually impaired pedestrians — running entirely in the browser, no special hardware required.**

VisionAid combines on-device object detection, classical computer vision, GPS/compass navigation, and conversational AI to give visually impaired users real-time, spoken awareness of their surroundings — using nothing more than a smartphone.

🔗 **Live Demo:** [visionaid-alpha.vercel.app](https://visionaid-alpha.vercel.app/)

---

## The Problem

Visually impaired pedestrians face a critical gap in independent outdoor mobility: traditional aids like a cane or guide dog provide physical feedback but no information about *what* is ahead, *how far away* it is, or *whether it's moving toward you*. Existing high-tech solutions are often expensive, require specialized hardware, or don't work outdoors in unstructured environments like public streets.

## The Solution

VisionAid turns any smartphone into a real-time environmental awareness system — entirely through a web browser. Point the camera ahead, and it tells you what's there, how far, and how to get where you're going, all through voice.

---

## Features

### 🚗 Real-Time Object Detection
On-device YOLOv8 object detection (via ONNX Runtime Web) identifies pedestrians, vehicles, bicycles, motorcycles, traffic lights, and more directly in the browser — no server round-trip, works offline once loaded. Distance is estimated using real-world object size vs. apparent size in frame, with the closest/most safety-relevant objects announced first.

### 🛣️ Classical Computer Vision Road-Hazard Detection
Independent of the AI model, OpenCV-based checks (running in a background Web Worker so the UI never freezes) detect:
- Zebra crossings, via Canny edge detection + Hough line transforms
- Wet/reflective road surfaces, via color-space thresholding
- Low-light conditions, via brightness analysis

### 🧭 GPS + Compass Waypoint Navigation
Tap a destination on an interactive map and get live, turn-by-turn spoken directions that adjust in real time based on GPS position and device compass heading — instructions are relative to which way you're actually facing, not just north.

### 🗣️ Voice Control & Conversational AI
The entire app is operable hands-free — ask what's ahead, request a sign to be read aloud, check for a crossing, or ask an open-ended question and get a natural spoken answer via AI transcription and response.

### 🆘 Emergency SOS
One tap (or one voice command) shares live GPS location and last known surroundings with a pre-configured emergency contact.

### 👤 Accounts & Persistence
Secure signup/login (bcrypt + JWT) with a SQLite-backed database storing scan history, saved locations, and preferences per account — data follows the user across devices.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Frontend | React, Tailwind CSS |
| Object Detection | YOLOv8n, ONNX Runtime Web (on-device inference) |
| Computer Vision | OpenCV.js (Web Worker) |
| AI Services | Groq (vision), Google Gemini (chat/transcription) |
| Navigation | OSRM (routing), Leaflet (maps) |
| Backend | Node.js, Express |
| Database | SQLite (better-sqlite3) |
| Auth | JWT, bcrypt |
| Alerts | EmailJS |
| Voice | Web Speech API |

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                     Browser (Client)                     │
│                                                            │
│  ┌──────────────┐  ┌───────────────┐  ┌────────────────┐ │
│  │ YOLOv8n via  │  │ OpenCV.js in  │  │ GPS + Compass   │ │
│  │ ONNX Runtime │  │ Web Worker    │  │ Sensors         │ │
│  │ (on-device)  │  │ (off-thread)  │  │                 │ │
│  └──────┬───────┘  └───────┬───────┘  └────────┬────────┘ │
│         └──────────────────┴───────────────────┘          │
│                            │                               │
└────────────────────────────┼───────────────────────────────┘
                              │
                    ┌─────────▼──────────┐
                    │  Node.js / Express  │
                    │  Backend API        │
                    └─────────┬──────────┘
                              │
        ┌─────────────────────┼─────────────────────┐
        │                     │                      │
  ┌─────▼─────┐      ┌────────▼────────┐    ┌────────▼────────┐
  │  SQLite   │      │  Groq / Gemini   │    │  OSRM Routing   │
  │ (users,   │      │  (scene desc.,   │    │  (directions)   │
  │ history)  │      │  chat, voice)    │    │                  │
  └───────────┘      └─────────────────┘    └──────────────────┘
```

Object detection and computer vision run **entirely client-side** — the only network calls are for optional AI scene descriptions, voice chat, and route planning, keeping the core safety features fast and functional even on unreliable connections.

---

## Why This Matters

Assistive technology for the visually impaired is often locked behind expensive, specialized hardware. VisionAid is built entirely on free and open frameworks, running on a device most people already own — a smartphone — making real-time environmental awareness technology dramatically more accessible.

---

## Getting Started

### Prerequisites
- Node.js 18+
- npm

### Installation

```bash
git clone https://github.com/yourusername/visionaid.git
cd visionaid

# Backend
cd backend
npm install
cp .env.example .env   # fill in your API keys
node server.js

# Frontend (new terminal)
cd frontend
npm install
npm run dev
```

### Environment Variables

See `backend/.env.example` for required keys — you'll need at least one Groq API key (vision) and one Gemini API key (chat/transcription/voice).

---

## Roadmap

- [ ] Custom-trained pothole/road-damage detection model
- [ ] Offline-first PWA support
- [ ] Multi-language voice support beyond English/Hindi
- [ ] Wearable/hardware integration (bone-conduction headset support)

---

## License

MIT

---

## Acknowledgments

Built with YOLOv8 (Ultralytics), OpenCV, ONNX Runtime, Groq, and Google Gemini.
