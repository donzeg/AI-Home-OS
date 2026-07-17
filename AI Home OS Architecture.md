# AI Home OS (Draft Architecture)

## Vision
Build a proactive AI-powered home that can perceive, remember, reason, converse, and act autonomously.

## Core Principles
- Context over simple automation
- Privacy-first where possible
- Modular services
- Home Assistant as orchestration layer
- AI models for reasoning, not direct device control

# Architecture

## Layer 1 – Perception
### Cameras
- PoE ONVIF cameras
- Frigate for object/person detection
- Doorbell camera

### Presence
- mmWave presence sensors (Aqara FP2, Everything Presence Lite, Apollo MTR-1)
- PIR sensors
- Door/window sensors
- BLE and Wi-Fi device tracking

### Environmental
- Temperature/Humidity
- CO2
- VOC/Air quality
- Illuminance
- Water leak sensors
- Smoke/CO alarms

### Energy
- Smart electricity meters
- Solar inverter integration
- Battery SOC
- Smart plugs with energy monitoring

### Audio
- Ceiling microphones or room microphones
- Multi-room speakers

## Layer 2 – Memory
Store:
- Occupants
- Preferences
- Daily routines
- Conversation summaries
- Device states
- Historical events

Suggested storage:
- PostgreSQL
- Vector database for semantic memory

## Layer 3 – Reasoning
LLM receives:
- Current sensor state
- Recent history
- User preferences
- Calendar
- Weather
- Energy state

Outputs:
- Recommended actions
- Spoken responses
- Notifications

## Layer 4 – Action
Controlled via Home Assistant:
- Lights
- Curtains
- HVAC
- Locks
- Garage/Gate
- Irrigation
- Audio
- TV
- Metering platform

## Layer 5 – Conversation
Wake word or continuous presence.
Conversation examples:
- "Welcome home."
- "You have a meeting in 30 minutes."
- "Solar production is low; delaying washing machine."

# Suggested Hardware

## Networking
- UniFi or Omada PoE switches
- VLAN-capable router

## Compute
- Intel NUC (AI server)
- Optional NVIDIA GPU or Coral TPU

## Sensors
- Aqara FP2
- Apollo Automation sensors
- Zooz/Z-Wave door sensors
- Shelly relays
- ESPHome custom sensors

## Cameras
- ONVIF PoE cameras
- Google Nest Doorbell

## Audio
- HomePods / Nest Audio / ceiling speakers
- ESP32 microphone satellites

## Smart Devices
- Smart locks
- Smart blinds
- Smart thermostats
- Smart plugs
- Smart relays

# Software Stack
- Home Assistant
- Frigate
- ESPHome
- MQTT
- Node-RED (optional)
- PostgreSQL
- Redis
- Ollama (local) and/or GPT/Claude/Gemini
- Whisper STT
- Piper TTS

# Future Vision
Introduce specialized agents:
1. Perception Agent
2. Memory Agent
3. Planning Agent
4. Energy Agent
5. Security Agent
6. Conversation Agent

Each agent communicates through an event bus while Home Assistant executes approved actions.
