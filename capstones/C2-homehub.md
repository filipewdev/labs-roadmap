# Capstone II — HomeHub

> **Type**: Capstone (multi-month build, not a weekend lab)
> **Purpose**: Self-hosted home automation and IoT monitoring platform
> **Stack**: Flutter (mobile) + TypeScript (backend) + Python (Pi edge) + C++ (Arduino firmware)
> **Build after**: Labs 05, 06, 07, 08, 18, 21, 22, 23, 24, 25

---

## Overview

HomeHub is a self-hosted home automation platform that runs on your Raspberry Pi.
It's the physical counterpart to the therapy platform — one demonstrates cloud/SaaS
skills, this one demonstrates systems/embedded/mobile skills.

**What it does:**
- Live sensor readings (temperature, humidity, motion) from Arduino nodes
- YOLOv8 vision detection on Pi Camera (detect people, packages at door)
- Flutter mobile app — local-first, works on home WiFi without internet
- Automation rules ("if temperature > 30°C and I'm home → notification")
- Full MQTT infrastructure with multiple device nodes
- Everything monitored in Grafana

---

## System Architecture

```
Physical Layer
├── Arduino Nano (room 1): DHT22 temp/humidity + PIR motion
├── Arduino Nano (room 2): DHT22 + light sensor
└── Raspberry Pi 5 (hub):
    ├── Pi Camera v3 (front door)
    ├── MQTT broker (Mosquitto)
    ├── Edge ML inference (YOLOv8 ONNX INT8)
    └── All backend services in Docker Compose

MQTT Message Bus (LAN)
├── sensors/room1/telemetry    → Arduino 1 → Python bridge
├── sensors/room2/telemetry    → Arduino 2 → Python bridge
├── vision/door/detections     → Pi Camera → YOLOv8 → MQTT
└── commands/room1/+           → Flutter app → Arduino 1

Backend Services (Pi, Docker Compose)
├── API Server (TypeScript/Fastify): REST + WebSocket
├── MQTT Bridge (Python): serial → MQTT
├── Vision Service (Python): camera + YOLO + MQTT
├── Automation Engine (TypeScript): rule evaluation
├── PostgreSQL: sensor history, rules, audit log
├── Redis: latest readings cache, presence state
└── Nginx: reverse proxy, local SSL

Flutter Mobile App
├── Local-first (Drift SQLite): works without internet
├── Real-time WebSocket: live sensor values
├── Biometric lock: Face ID / fingerprint (Lab 21)
├── Haptic feedback on control actions
├── Background sync every 15 minutes (Lab 22)
└── Platform channels: battery/network status
```

---

## Automation Engine

```typescript
interface AutomationRule {
  id: string
  name: string
  trigger: Trigger         // MQTT message, schedule, sensor threshold
  conditions: Condition[]  // AND/OR logic, time ranges, presence
  actions: Action[]        // MQTT publish, WhatsApp notification, HTTP call
  enabled: boolean
}

// Example rules:
// "Hot room" — if room1 temperature > 32°C and time is 08:00-22:00 → WhatsApp + fan on
// "Person at door" — if vision detects person with confidence > 0.7 → WhatsApp + snapshot
// "Good morning" — every day at 07:30 → send weather + Selic rate via WhatsApp
```

---

## Flutter App: Key Screens

```
┌─────────────────────────────────────────────────┐
│  HomeHub                         [🔒 Biometric] │
├─────────────────────────────────────────────────┤
│  Home Overview                                  │
│  ┌──────────┐  ┌──────────┐                    │
│  │  Room 1  │  │  Room 2  │                    │
│  │  24.5°C  │  │  22.1°C  │                    │
│  │  65% RH  │  │  58% RH  │                    │
│  │  🟢 Occ  │  │  ⚪ Empty│                    │
│  └──────────┘  └──────────┘                    │
├─────────────────────────────────────────────────┤
│  Front Door  [🔒 Biometric to unlock camera]   │
├─────────────────────────────────────────────────┤
│  Automations: 3 active                          │
│  Last fired: "Person at door" — 14:32           │
└─────────────────────────────────────────────────┘
```

Real-time WebSocket updates when on home WiFi.
Last-known values from local SQLite when away.

---

## Open API Integration

**Open-Meteo** used for:
- Compare Arduino DHT22 readings against official Brasília weather
- "Good morning" automation: outdoor temperature context
- Surface sensor calibration drift over time

**BACEN** used for:
- Morning digest automation: Selic rate and USD/BRL in the daily summary

---

## What Each Lab Contributes

| Lab | What it adds to HomeHub |
|---|---|
| 05 Nginx | Reverse proxy for all services, local SSL |
| 06 Prometheus + Grafana | Dashboard showing all sensor data + ML inference FPS |
| 07 Loki | Log aggregation from all Docker services |
| 08 PostgreSQL | Sensor history, time-series queries, BRIN indexes |
| 18 Linux Admin | The Pi setup, systemd services, fail2ban |
| 21 Flutter Biometric | Lock screen for the mobile app, camera unlock |
| 22 Flutter Local-First | Offline mode when away from home WiFi |
| 23 Raspberry Pi Server | The hub itself |
| 24 Arduino + MQTT | Sensor nodes, the broker, the bridge |
| 25 Edge ML | YOLOv8 on the Pi camera |

---

## Build Milestones

- [ ] M1: MQTT broker + first Arduino node sending data to Grafana
- [ ] M2: Pi bridge + PostgreSQL persistence + REST API
- [ ] M3: Flutter app — basic sensor dashboard on home WiFi
- [ ] M4: Pi Camera + YOLOv8 ONNX + MQTT detection events
- [ ] M5: Automation engine — first rule firing (temperature alert)
- [ ] M6: Flutter — biometric lock + offline sync
- [ ] M7: Second Arduino node + multi-room support
- [ ] M8: n8n integration for WhatsApp notifications
- [ ] M9: Full Grafana dashboard (sensors + vision + system)
- [ ] M10: Demo video recorded — the portfolio showpiece

---

## Portfolio Statement

> "I built a self-hosted home automation platform running on a Raspberry Pi.
> The system collects sensor data from Arduino nodes via MQTT, runs YOLOv8
> object detection on a Pi Camera at 15fps using a quantized ONNX model,
> and exposes everything through a Flutter app with biometric authentication
> and offline-first local sync. An automation engine evaluates rules across
> sensor streams and triggers WhatsApp notifications for relevant events.
> The entire stack is containerized with Docker, monitored with Prometheus
> and Grafana, and every component was built through a dedicated lab first —
> from the C++ firmware on the Arduino to the Dart UI on my phone."
