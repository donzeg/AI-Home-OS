# Chapter 03 — Vision System

**AI Home OS Internal Design Specification**  
**Classification:** Internal — Engineering  
**Status:** Draft v1.0  
**Date:** 2026-07-17

---

## Table of Contents

1. [Overview](#1-overview)
2. [Design Philosophy](#2-design-philosophy)
3. [Camera Architecture](#3-camera-architecture)
4. [Camera Hardware Selection](#4-camera-hardware-selection)
5. [Camera Placement Strategy](#5-camera-placement-strategy)
6. [Frigate NVR — Core Vision Platform](#6-frigate-nvr--core-vision-platform)
7. [Object Detection Pipeline](#7-object-detection-pipeline)
8. [Facial Recognition](#8-facial-recognition)
9. [License Plate Recognition (LPR)](#9-license-plate-recognition-lpr)
10. [Activity Understanding](#10-activity-understanding)
11. [Pet Detection](#11-pet-detection)
12. [Package Detection](#12-package-detection)
13. [Intrusion Detection](#13-intrusion-detection)
14. [Video Summarization](#14-video-summarization)
15. [Privacy Architecture](#15-privacy-architecture)
16. [Recording Policies & Retention](#16-recording-policies--retention)
17. [Storage Architecture](#17-storage-architecture)
18. [AI Inference Architecture](#18-ai-inference-architecture)
19. [Vision Event Pipeline](#19-vision-event-pipeline)
20. [Integration with AI Home OS](#20-integration-with-ai-home-os)
21. [Camera BOM](#21-camera-bom)
22. [Design Decisions & Trade-offs](#22-design-decisions--trade-offs)
23. [Failure Modes & Redundancy](#23-failure-modes--redundancy)
24. [Risks](#24-risks)
25. [Future Improvements](#25-future-improvements)
26. [References](#26-references)

---

## 1. Overview

The vision system is the AI's eyes. It transforms raw video streams into structured, actionable intelligence — answering questions that no sensor can: Who is at the front door? Is the person in the driveway a known visitor or a stranger? Did the package arrive? Is the child playing safely in the garden? Did someone leave a window open?

Unlike conventional CCTV systems — which record footage for human review after an incident — the AI Home OS vision system operates in real time, continuously processing video through multiple AI inference pipelines and feeding structured event data to the Context Engine.

This chapter does not design a surveillance system. It designs a **visual understanding system** that happens to record video as a byproduct.

### Key Capabilities

| Capability | Description |
|-----------|-------------|
| **Object detection** | People, vehicles, animals, packages — real-time, every camera |
| **Facial recognition** | Known occupants, registered visitors, unknown persons |
| **License plate recognition** | Known and unknown vehicles entering property |
| **Activity understanding** | What is the detected person doing? |
| **Intrusion detection** | Presence in zones that should be empty |
| **Package detection** | Delivery arrival and theft monitoring |
| **Pet detection** | Where are the pets? Are they safe? |
| **Video summarization** | Daily digest of key events |
| **Privacy masking** | Redact areas from live and recorded feeds |
| **Anomaly detection** | Events that are statistically unusual |

### What the Vision System Is NOT

- It is **not** a cloud surveillance service — all processing is local
- It is **not** a law enforcement tool — facial recognition data stays within the home
- It is **not** always-on audio recording — cameras do not capture audio (audio is in Chapter 4)
- It is **not** a replacement for physical security measures — it is a detection and notification layer

---

## 2. Design Philosophy

### 2.1 Local-First Inference

All AI inference runs on the local compute node (AI server with GPU). Video streams **never** leave the home network for AI processing. Cloud is used only for:
- Remote viewing via encrypted VPN (user-initiated, not automatic)
- Offsite backup of important events (encrypted, user-configured, optional)

### 2.2 Event-Driven, Not Continuous Storage

Recording everything continuously from 12 cameras at 4MP consumes approximately 4–8 TB per week. AI Home OS instead applies **intelligent recording policies**:
- Record 15–30 seconds before and after any detected event
- Keep continuous low-resolution recording for 24 hours as rolling buffer (for incident review)
- Keep high-resolution clips of detected events for configurable retention period
- Summarize and discard routine events

### 2.3 Privacy by Design

Every camera's footage is subject to explicit privacy rules:
- Bedroom cameras are **prohibited by default** (opt-in only, for elderly care)
- All camera feeds can be **temporarily blinded** by the primary occupant (e.g., "privacy mode")
- Guests can opt out of facial recognition and have their face blurred in recordings
- All stored footage is **encrypted at rest** (AES-256)

### 2.4 Accuracy Over Speed

A false positive (alerting user about a "intruder" that is a shadow or neighbor's cat) destroys trust in the system far faster than a slow alert. The vision pipeline uses **multi-stage confirmation**:

```
Stage 1: Motion detection (fast, near-zero CPU)
    ↓ (if motion detected)
Stage 2: Object detection YOLO (fast, GPU)
    ↓ (if person/vehicle detected)
Stage 3: Identity confirmation (slower, GPU — facial/plate recognition)
    ↓ (if identity confirmed or unknown)
Stage 4: Context fusion (sensor data cross-reference)
    ↓
Stage 5: Decision & notification (alert or log silently)
```

---

## 3. Camera Architecture

### 3.1 System Architecture Overview

```mermaid
flowchart TD
    subgraph CAMERAS["Camera Layer — VLAN 40"]
        CAM1["Front Door\n4MP PoE\nONVIF/RTSP"]
        CAM2["Driveway\n8MP 4K PoE\nONVIF/RTSP"]
        CAM3["Backyard\n4MP PoE\nONVIF/RTSP"]
        CAM4["Side Gate\n4MP PoE"]
        CAM5["Garage Exterior\n4MP PoE"]
        CAM6["Living Room\n2MP Indoor\nOptional"]
        CAM7["Kitchen\n2MP Indoor\nOptional"]
        CAM8["Driveway wide\n180° fisheye"]
    end

    subgraph COMPUTE["AI Server — VLAN 20"]
        FRIGATE["Frigate NVR\nDocker Container"]
        GPU["NVIDIA RTX 4070\n12 GB VRAM"]
        YOLO["YOLO v9 / v10\nObject Detection"]
        FR["DeepFace\nFacial Recognition"]
        LPR["CodeProject.AI / OpenALPR\nLicense Plate"]
        ACTIVITY["Activity Classifier\nCustom Model"]
    end

    subgraph STORAGE["Storage Layer"]
        NVMe["NVMe SSD\nFrigate DB + Clips"]
        NAS["NAS — HDD RAID\nLong-term recordings"]
    end

    subgraph INTEGRATION["AI Home OS"]
        MQTT["MQTT Broker\nEvent publication"]
        CTX["Context Engine"]
        AGENTS["AI Agents"]
    end

    CAM1 -->|"RTSP stream"| FRIGATE
    CAM2 -->|"RTSP stream"| FRIGATE
    CAM3 -->|"RTSP stream"| FRIGATE
    CAM4 -->|"RTSP stream"| FRIGATE
    CAM5 -->|"RTSP stream"| FRIGATE
    CAM6 -->|"RTSP stream"| FRIGATE
    CAM7 -->|"RTSP stream"| FRIGATE
    CAM8 -->|"RTSP stream"| FRIGATE

    FRIGATE --> GPU
    GPU --> YOLO
    GPU --> FR
    GPU --> LPR
    GPU --> ACTIVITY

    FRIGATE --> NVMe
    NVMe -->|"Daily sync"| NAS

    FRIGATE -->|"Detection events\nhomeios/cameras/..."| MQTT
    MQTT --> CTX
    CTX --> AGENTS
```

### 3.2 Protocol Stack

| Protocol | Role | Notes |
|----------|------|-------|
| **ONVIF** | Camera discovery, PTZ control, event subscription | Industry standard; used for initial config and control |
| **RTSP** | Video stream delivery (H.264/H.265) | Primary stream delivery protocol |
| **HTTP/HTTPS** | Camera web interface, snapshot API | Management and snapshot capture |
| **MQTT** | Detection events from Frigate → AI Home OS | Bidirectional: events out, commands in |
| **WebSocket** | Live view in mobile app and wall panels | Low-latency web streaming via Frigate go2rtc |

### 3.3 Stream Configuration

Each camera provides multiple streams optimized for different uses:

| Stream | Resolution | Frame Rate | Codec | Bitrate | Use |
|--------|-----------|-----------|-------|---------|-----|
| **Main stream** | 2560×1440 (4MP) | 15 fps | H.265 | 2–4 Mbps | Recording, high-quality snapshots |
| **Sub stream** | 640×480 | 5 fps | H.264 | 256–512 kbps | AI inference (Frigate detection stream) |
| **Snapshot** | Full resolution | On demand | JPEG | N/A | Alert snapshots, facial recognition |

> **Critical design decision:** Frigate should run object detection on the **sub stream** (low resolution), not the main stream. YOLO inference on 640×480 frames uses far less GPU than 4MP frames and achieves near-identical detection accuracy for people/vehicles. The high-resolution main stream is used only when a detection snapshot is needed (triggered after detection on sub stream).

---

## 4. Camera Hardware Selection

### 4.1 Selection Criteria

| Criterion | Requirement |
|-----------|------------|
| Protocol | ONVIF Profile S + RTSP mandatory |
| Codec | H.265 (HEVC) required for storage efficiency |
| Sub-stream | Must support independent low-resolution stream |
| IR night vision | Minimum 20m range for exterior cameras |
| Power | PoE 802.3af (15.4W) minimum; 802.3at (25.5W) for PTZ or heater |
| Weatherproofing | IP67 minimum for all exterior cameras |
| Vandal resistance | IK10 for any camera reachable from ground level |
| Local RTSP | Must work without manufacturer cloud account |
| Privacy | No mandatory cloud; no forced telemetry |

> **Warning on cloud-mandatory cameras:** Cameras from certain manufacturers (Arlo, Wyze Gen1) require cloud connectivity to function. These are categorically **incompatible** with AI Home OS. Never deploy cameras that will not stream RTSP locally without a cloud account.

### 4.2 Recommended Camera Models

**Outdoor Fixed Cameras:**

| Camera | Resolution | IR | WDR | PoE | Cost | Best For |
|--------|-----------|-----|-----|-----|------|---------|
| **Reolink RLC-810A** | 8MP 4K | 30m | 120dB | 802.3af | $55 | Driveway, wide coverage |
| **Amcrest IP8M-2483EW** | 8MP 4K | 100ft | 120dB | 802.3af | $100 | Front door, high detail |
| **Hikvision DS-2CD2T47G2-L** | 4MP | ColorVu 60m | 120dB | 802.3at | $120 | Color night vision areas |
| **Dahua IPC-HFW2849S-S-IL** | 8MP | Smart Dual Light | 120dB | 802.3af | $90 | General exterior |
| **Reolink RLC-823A** | 8MP + spotlight | 30m color | 120dB | 802.3at | $80 | Deterrence + visibility |

**Outdoor with Color Night Vision (No IR, White Light):**

| Camera | Technology | Cost | Notes |
|--------|-----------|------|-------|
| **Hikvision ColorVu** | Full-color night via large aperture + supplemental light | $100–150 | Excellent color night vision |
| **Dahua Smart Dual Light** | IR + warm white light combination | $80–120 | Best combination — IR for distance, white light for ID |

**Indoor Cameras (Optional — see Privacy section):**

| Camera | Resolution | FOV | Cost | Notes |
|--------|-----------|-----|------|-------|
| **Reolink E1 Zoom** | 5MP | 3× optical zoom | $45 | Pan/tilt, good for living areas |
| **Amcrest AD410** | 4MP | Fixed 110° | $65 | Doorbell alternative, indoor |
| **Frigate-compatible generic** | 2MP | 110° | $25 | Budget indoor monitoring |

**Doorbell Camera:**

| Camera | Protocol | Resolution | Cost | Notes |
|--------|----------|-----------|------|-------|
| **Reolink Video Doorbell PoE** | RTSP/ONVIF + MQTT | 5MP | $70 | Best: fully local, PoE, RTSP |
| **Amcrest AD410** | RTSP/ONVIF | 4MP | $80 | Local, no subscription |
| **Hikvision DS-KV8213-WME1** | ONVIF + SIP | 2MP | $150 | Professional, SIP intercom |
| **Google Nest Doorbell (wired)** | Proprietary + HA | 3MP | $100 | Cloud-dependent; not recommended |

> **Doorbell recommendation:** **Reolink Video Doorbell PoE** — fully local RTSP, PoE-powered, no subscription, native Frigate integration, MQTT button press events. Avoid any doorbell requiring a cloud account for basic function.

**PTZ (Pan-Tilt-Zoom) Cameras:**

| Camera | Resolution | Zoom | Cost | Notes |
|--------|-----------|------|------|-------|
| **Reolink RLC-823A PTZ** | 8MP | 5× optical | $120 | ONVIF PTZ control |
| **Hikvision DS-2DE4425IWG-E** | 4MP | 25× optical | $350 | Professional PTZ, ONVIF |
| **Dahua PTZ23230U** | 2MP | 30× optical | $400 | Long-range tracking |

> PTZ cameras are deployed sparingly — driveway and garden perimeter. Their auto-tracking feature (person detected → camera rotates to follow) is managed by Frigate + ONVIF PTZ commands from the AI server.

### 4.3 Fisheye / 180°–360° Cameras

For large open areas (garage interior, large living room, main entrance hall), a single fisheye camera provides full coverage with no blind spots:

| Camera | Resolution | FOV | Cost |
|--------|-----------|-----|------|
| **Hikvision DS-2CD2955G1-ISU** | 5MP | 180° fisheye | $150 |
| **Reolink FE-W** | 5MP | 360° fisheye | $70 |
| **Dahua IPC-EBW81242** | 12MP | 360° | $250 |

Fisheye correction (dewarping) is handled by Frigate's built-in dewarping or the go2rtc streaming server before presenting to the viewer.

---

## 5. Camera Placement Strategy

### 5.1 Coverage Philosophy

```
Coverage principles:

1. Every entry/exit point must be covered with at least TWO camera angles
   (one wide for context, one close for identification)

2. All exterior zones must be covered with overlapping fields of view
   (no blind spots larger than 2m × 2m)

3. Cameras must identify a face at the primary entry point from at least 10m

4. All ground-floor windows must be visible from at least one camera

5. Interior cameras are OPTIONAL and require explicit user consent per room
```

### 5.2 Reference Placement — 4-Bedroom Home

```
Top-down view (conceptual):

                        [Street]
                           │
              ┌────────────┼────────────┐
              │            │            │
         [Gate]      [Driveway]    [Mailbox]
              │       CAM-02          CAM-M
              │      (8MP 4K)      (mailbox)
              │
    ┌─────────┼──────────────────────────────┐
    │         │                              │
    │   CAM-01 (Front Door)    CAM-05 (Garage)
    │   (Doorbell 5MP)         (4MP exterior)
    │                                        │
    │         [HOUSE INTERIOR]               │
    │                                        │
    │   CAM-03 (Living Room)                 │
    │   (optional, indoor 2MP)               │
    │                                        │
    │                          CAM-06 (Side) │
    │                          (4MP wide)    │
    │         [BACKYARD]                     │
    │                                        │
    │   CAM-04 (Garden/Pool)                 │
    │   (8MP 4K, wide angle)                 │
    └────────────────────────────────────────┘
```

### 5.3 Camera Field of View Planning

Before installation, every camera position must be validated using FOV simulation:

| Tool | Purpose |
|------|---------|
| **JVSG IP Video System Design Tool** | Professional camera placement simulation |
| **Camera FoV Calc** (online) | Quick FOV calculation by focal length and resolution |
| **Physical test mount** | Mount camera on temporary pole at planned position before drilling |

**Key FOV specifications:**

| Location | Required FOV | Recommended Focal Length | Min Resolution |
|----------|-------------|--------------------------|---------------|
| Front door (ID focus) | 60°–90° | 4mm | 4MP |
| Driveway (LPR) | 30°–60° | 6–8mm | 8MP |
| Backyard (wide) | 90°–120° | 2.8mm | 4MP |
| Side gate | 60°–80° | 4mm | 4MP |
| Garage interior | 100°–120° fisheye | Fisheye | 5MP |
| Living room | 100°–120° | 2.8mm | 2MP indoor |

### 5.4 Mounting Height and Angle

| Location Type | Mounting Height | Tilt Angle | Notes |
|---------------|----------------|-----------|-------|
| Exterior corner (wall) | 3.0–4.0m | 15–25° down | High enough to be tamper-resistant |
| Doorbell | 1.8–2.2m | Slight down angle | Face capture optimized |
| Garage soffit | 2.5–3.0m | 30° down | Under eave for weather protection |
| Interior corner | 2.5m | 15° down | Wide angle at corners for full room coverage |
| PTZ pedestal | 3.0–4.0m | 0° (auto-pan) | Clear rotation radius |

---

## 6. Frigate NVR — Core Vision Platform

### 6.1 What Frigate Does

Frigate is an open-source NVR (Network Video Recorder) built specifically for AI object detection integration. It provides:

- RTSP stream ingestion from all cameras
- Motion detection (fast, CPU-only — triggers GPU detection)
- Real-time object detection via YOLO models (GPU or Coral TPU)
- Event recording (clips triggered by detections)
- Snapshot capture on detection
- MQTT event publication for AI Home OS integration
- HTTP API for external access to events and snapshots
- go2rtc integration for low-latency live viewing

### 6.2 Frigate Configuration Architecture

```yaml
# frigate.yml — Reference configuration (abbreviated)

mqtt:
  host: 192.168.20.10
  port: 1883
  user: frigate
  password: "{FRIGATE_MQTT_PASSWORD}"
  topic_prefix: homeios/cameras

database:
  path: /config/frigate.db

detectors:
  nvidia_gpu:
    type: tensorrt
    device: 0  # RTX 4070

# Alternatively, Coral TPU:
# detectors:
#   coral:
#     type: edgetpu
#     device: pci  # or usb

model:
  path: /config/model_cache/yolov9c.trt  # TensorRT compiled model
  input_tensor: nhwc
  input_pixel_format: rgb
  width: 640
  height: 640

objects:
  track:
    - person
    - car
    - motorcycle
    - bicycle
    - dog
    - cat
    - bird
    - package  # requires custom model or fine-tuned weights
  filters:
    person:
      min_area: 1500        # pixels² — ignore tiny detections
      min_score: 0.65       # 65% confidence minimum
      max_ratio: 10         # ignore unrealistically tall/narrow bboxes
    car:
      min_area: 5000
      min_score: 0.7

record:
  enabled: true
  retain:
    days: 3                 # Keep all recordings 3 days
    mode: motion
  events:
    retain:
      default: 30           # Keep event clips 30 days
      objects:
        person: 60          # Person events kept 60 days
        car: 30

snapshots:
  enabled: true
  bounding_box: true
  crop: true
  quality: 95
  retain:
    default: 30

cameras:
  front_door:
    ffmpeg:
      inputs:
        - path: rtsp://admin:{CAM_PASSWORD}@192.168.40.10:554/stream1
          roles: [record]    # High-res: record only
        - path: rtsp://admin:{CAM_PASSWORD}@192.168.40.10:554/stream2
          roles: [detect]    # Low-res: AI detection only
    detect:
      enabled: true
      width: 640
      height: 480
      fps: 5
    motion:
      mask:
        - "0,0,640,100"    # Mask top 100px (sky/trees moving in wind)
    zones:
      doorstep:
        coordinates: "280,480,480,480,480,300,280,300"
      driveway_approach:
        coordinates: "0,480,640,480,640,200,0,200"

  driveway:
    ffmpeg:
      inputs:
        - path: rtsp://admin:{CAM_PASSWORD}@192.168.40.11:554/stream1
          roles: [record]
        - path: rtsp://admin:{CAM_PASSWORD}@192.168.40.11:554/stream2
          roles: [detect]
    detect:
      enabled: true
      width: 1280           # Higher res for LPR
      height: 720
      fps: 10               # Higher FPS for vehicle movement
```

### 6.3 Frigate Zones

Zones are polygonal regions within a camera's field of view. The AI uses zones to add spatial context to detections:

```
Camera: front_door
Zones:
  ┌──────────────────────────────────────┐
  │                 [sky_mask]           │
  │─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─│
  │                                      │
  │    [driveway_approach]               │
  │                                      │
  │─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ ─│
  │              [doorstep]              │
  └──────────────────────────────────────┘

MQTT events include zone:
  "person entered driveway_approach"  → long-range alert
  "person entered doorstep"           → doorbell alert
  "person exited doorstep"            → delivery departed
```

### 6.4 go2rtc Streaming

go2rtc is bundled with Frigate and provides:
- WebRTC streaming (< 200ms latency) for live wall panel and mobile app view
- HLS streaming for broader compatibility
- Re-streaming: transforms RTSP to WebRTC without re-encoding (GPU-free)

```yaml
# go2rtc config (inside Frigate)
streams:
  front_door:
    - rtsp://admin:{CAM_PASSWORD}@192.168.40.10:554/stream1
  driveway:
    - rtsp://admin:{CAM_PASSWORD}@192.168.40.11:554/stream1
```

---

## 7. Object Detection Pipeline

### 7.1 Model Selection

| Model | mAP | Speed (RTX 4070) | VRAM | Best For |
|-------|-----|-----------------|------|---------|
| **YOLOv9-C** | 53.0 | ~10ms/frame | 2 GB | Best accuracy/speed balance |
| **YOLOv10-M** | 51.3 | ~7ms/frame | 1.5 GB | Very fast, slightly lower accuracy |
| **YOLOv8-L** | 52.9 | ~12ms/frame | 2.5 GB | Mature, excellent Frigate support |
| **YOLOv8-N** | 37.3 | ~2ms/frame | 0.5 GB | Coral TPU / very low resource |
| **RT-DETR** | 54.8 | ~25ms/frame | 4 GB | Highest accuracy, transformer-based |

> **Recommendation:** Use **YOLOv10-M** as the default detection model for Frigate — optimized speed at 5–10 FPS per camera with excellent accuracy for person/vehicle/animal detection. Compile to TensorRT for the RTX 4070 for maximum throughput. If running Coral TPU, use **YOLOv8-N** (nano) compiled to EdgeTPU format.

### 7.2 GPU Resource Allocation — Multi-Camera Inference

```
RTX 4070 12 GB VRAM allocation:

  Detection (Frigate + YOLO × 8 cameras):  ~2 GB
  Facial Recognition (DeepFace batch):       ~1 GB
  LPR (OpenALPR/CodeProject):               ~0.5 GB
  Ollama LLM (Llama 3.3 8B):               ~8 GB
  ─────────────────────────────────────────────────
  Total:                                   ~11.5 GB  ← tight on 12 GB

  → If VRAM constrained: run LLM on CPU/RAM and dedicate GPU to vision
  → If budget allows: second GPU (RTX 3060 12 GB) dedicated to LLM
```

### 7.3 Detection Processing Pipeline

```mermaid
sequenceDiagram
    participant CAM as Camera (RTSP)
    participant FFmpeg as FFmpeg decoder
    participant MD as Motion Detector
    participant GPU as GPU / YOLO
    participant DB as Frigate DB
    participant FR as Face Recognition
    participant MQTT as MQTT Broker
    participant CTX as Context Engine

    CAM->>FFmpeg: H.265 encoded stream (sub stream, 640×480 @ 5fps)
    FFmpeg->>MD: Decoded frame
    MD->>MD: Pixel diff motion detection (CPU, ~1ms)
    alt Motion detected
        MD->>GPU: Frame batch (up to 8 cameras simultaneously)
        GPU->>GPU: YOLO inference (TensorRT, ~10ms per batch)
        GPU-->>DB: Detected objects + bounding boxes + confidence
        GPU-->>FR: Person crop → face recognition queue
        DB->>FFmpeg: Trigger: start recording main stream
        GPU->>MQTT: homeios/cameras/{camera}/events\n{object: "person", zone: "doorstep", score: 0.91}
        MQTT->>CTX: Vision event received
    else No motion
        MD->>MD: Skip GPU inference (saves ~80% GPU cycles)
    end
    FR->>MQTT: homeios/cameras/{camera}/identity\n{person_id: "sadiq", confidence: 0.87}
```

### 7.4 Inference Throughput Calculation

For 8 cameras at 5 FPS each on the RTX 4070:

```
Total frames/second:  8 cameras × 5 fps = 40 fps
YOLO inference time:  ~10ms per batch (8 frames)
Batch throughput:     8 frames / 10ms = 800 fps theoretical
                      → Well within capacity; GPU is not the bottleneck

Bottleneck:  FFmpeg decoding CPU load
             8 × H.264 640×480 @ 5fps ≈ 2–3 CPU cores

Practical limit on 12-core CPU:
  → 20–25 cameras at 5fps (sub-stream) comfortably
  → 12–15 cameras at 10fps
  → 8 cameras at 15fps (smoother detection for fast-moving vehicles)
```

---

## 8. Facial Recognition

### 8.1 Architecture

Facial recognition in AI Home OS is a **two-stage pipeline**:

1. **Detection**: YOLO detects a person and crops the bounding box
2. **Recognition**: DeepFace (or equivalent) extracts a 128/512-dimensional face embedding and compares against the enrolled face database

```mermaid
flowchart LR
    YOLO["YOLO\nPerson detected\nCrop extracted"]
    FACE_DETECT["Face Detector\n(RetinaFace or\nMTCNN)"]
    ALIGN["Face Alignment\nLandmark detection\n5-point alignment"]
    EMBED["Embedding Model\n(ArcFace / FaceNet)\n512-d vector"]
    MATCH["Vector Similarity\nCosine distance\nvs enrolled database"]
    RESULT["Identity Result\n+ Confidence Score"]

    YOLO --> FACE_DETECT
    FACE_DETECT --> ALIGN
    ALIGN --> EMBED
    EMBED --> MATCH
    MATCH --> RESULT
```

### 8.2 Model Selection

| Stage | Model | Notes |
|-------|-------|-------|
| **Face detection** | RetinaFace | Best accuracy, handles small/occluded faces |
| **Embedding (recognition)** | ArcFace (ResNet100) | State-of-the-art accuracy (99.8% LFW benchmark) |
| **Alternative embedding** | FaceNet (Inception ResNet v2) | Slightly lower accuracy, faster |
| **Framework** | DeepFace (Python) | Wraps multiple models, easy integration |

### 8.3 Enrollment Process

```python
# Face enrollment pipeline (pseudo-code)

def enroll_person(person_id: str, name: str, photos: List[Image]) -> EnrollmentResult:
    """
    Enroll a new person into the facial recognition database.
    
    Args:
        person_id: Unique identifier (e.g., "sadiq")
        name: Display name
        photos: 10-30 photos from different angles, lighting, expressions
    """
    embeddings = []
    
    for photo in photos:
        # Detect and align face
        faces = retinaface.detect_faces(photo)
        if not faces or len(faces) > 1:
            continue  # Skip: no face or multiple faces in enrollment photo
        
        face_crop = align_face(photo, faces[0].landmarks)
        
        # Quality check
        if face_quality_score(face_crop) < 0.7:
            continue  # Skip blurry, poorly lit, or occluded faces
        
        # Generate embedding
        embedding = arcface_model.get_embedding(face_crop)
        embeddings.append(embedding)
    
    if len(embeddings) < 5:
        raise EnrollmentError("Insufficient quality photos — need at least 5")
    
    # Store mean embedding + individual embeddings for robust matching
    mean_embedding = np.mean(embeddings, axis=0)
    
    db.insert_face_profile(
        person_id=person_id,
        name=name,
        mean_embedding=mean_embedding,
        sample_embeddings=embeddings,
        enrolled_at=datetime.utcnow()
    )
    
    return EnrollmentResult(person_id=person_id, embedding_count=len(embeddings))
```

### 8.4 Recognition at Runtime

```python
# Runtime recognition (pseudo-code)

def recognize_face(face_crop: Image) -> RecognitionResult:
    # Quality gate
    if face_quality_score(face_crop) < 0.5:
        return RecognitionResult(identity="uncertain", confidence=0.0)
    
    # Generate embedding
    query_embedding = arcface_model.get_embedding(align_face(face_crop))
    
    # Compare against all enrolled profiles
    best_match = None
    best_distance = float("inf")
    
    for profile in db.get_all_face_profiles():
        distance = cosine_distance(query_embedding, profile.mean_embedding)
        if distance < best_distance:
            best_distance = distance
            best_match = profile
    
    # Threshold: cosine distance < 0.4 = same person (ArcFace)
    RECOGNITION_THRESHOLD = 0.40
    CONFIDENT_THRESHOLD = 0.25
    
    if best_distance < CONFIDENT_THRESHOLD:
        return RecognitionResult(
            identity=best_match.person_id,
            name=best_match.name,
            confidence=1.0 - best_distance,
            certainty="high"
        )
    elif best_distance < RECOGNITION_THRESHOLD:
        return RecognitionResult(
            identity=best_match.person_id,
            name=best_match.name,
            confidence=1.0 - best_distance,
            certainty="medium"
        )
    else:
        return RecognitionResult(
            identity="unknown",
            confidence=0.0,
            certainty="none"
        )
```

### 8.5 Face Recognition Database Schema

```sql
CREATE TABLE face_profiles (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    person_id       UUID REFERENCES persons(id) ON DELETE CASCADE,
    mean_embedding  VECTOR(512),      -- pgvector, ArcFace 512-d
    enrolled_at     TIMESTAMPTZ DEFAULT now(),
    last_seen       TIMESTAMPTZ,
    recognition_count INTEGER DEFAULT 0,
    is_active       BOOLEAN DEFAULT TRUE
);

CREATE TABLE face_samples (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    profile_id      UUID REFERENCES face_profiles(id) ON DELETE CASCADE,
    embedding       VECTOR(512),
    image_path      TEXT,             -- encrypted storage path
    quality_score   FLOAT,
    angle           VARCHAR(20),      -- 'frontal', 'left', 'right', 'up', 'down'
    lighting        VARCHAR(20),      -- 'bright', 'dim', 'backlit'
    created_at      TIMESTAMPTZ DEFAULT now()
);

-- Vector index for fast ANN search
CREATE INDEX ON face_profiles USING ivfflat (mean_embedding vector_cosine_ops)
    WITH (lists = 100);
```

### 8.6 Unknown Person Handling

When an unrecognized face is detected:

1. **First occurrence**: Log, capture snapshot, publish MQTT event with `identity: "unknown_001"`
2. **Repeated occurrences** (same face, multiple visits): Cluster embeddings, create "suspected person" profile
3. **User review**: Push notification with snapshot — "Unknown person at front door. [Add to contacts] [Dismiss] [Alert]"
4. **After user labels**: Add to face profile database with the provided name/relationship

```mermaid
flowchart TD
    UNKNOWN["Unknown face detected"]
    CLUSTER["Compare with existing\nunknown clusters"]
    NEW_CLUSTER{New or existing\nunknown profile?}
    CREATE["Create Unknown Profile\nunknown_XXX"]
    ADD_TO["Add face to\nexisting unknown cluster"]
    FREQ{Seen 3+ times\nin 7 days?}
    NOTIFY["Push notification:\nFrequent unknown visitor\n[snapshot] [options]"]
    ALERT["Standard alert:\nUnknown person detected"]

    UNKNOWN --> CLUSTER
    CLUSTER --> NEW_CLUSTER
    NEW_CLUSTER -->|New| CREATE
    NEW_CLUSTER -->|Existing| ADD_TO
    CREATE --> ALERT
    ADD_TO --> FREQ
    FREQ -->|Yes| NOTIFY
    FREQ -->|No| ALERT
```

---

## 9. License Plate Recognition (LPR)

### 9.1 Purpose

LPR enables the AI to:
- Automatically open the gate when a known vehicle approaches
- Alert when an unknown vehicle lingers near the property
- Log all vehicle visits with plate, timestamp, and duration
- Block access for specific plates (if gate control is implemented)

### 9.2 Camera Requirements for LPR

LPR accuracy depends heavily on camera angle, resolution, and capture speed:

| Parameter | Requirement |
|-----------|------------|
| Capture resolution | Minimum 8MP on the plate area (≥ 40 pixels/character) |
| Camera angle | 15–25° horizontal to plate (straight-on or slight angle) |
| Frame rate | 10+ FPS (to capture plate before vehicle passes) |
| Shutter speed | Fast enough to freeze a moving vehicle (< 1/1000s in daylight) |
| IR illumination | IR illuminator or white light for night capture |
| Distance | Best results at 3–10m from plate |

> The **driveway camera** must be positioned specifically for LPR — aimed at the plate height of approaching vehicles, not at the driver's face. Use a separate camera for face capture and a separate camera (or the same at the right angle) for LPR.

### 9.3 LPR Software

| Software | Type | Cost | Notes |
|----------|------|------|-------|
| **CodeProject.AI (ALPR module)** | Local, Docker | Free | Best free local option; OpenALPR engine |
| **OpenALPR** | Local, self-hosted | Free (open source) | Excellent accuracy, supports many countries |
| **Plate Recognizer** | Cloud + local agent | $5/month | Highest accuracy available, 50 plates free/month |
| **go2rtc + custom YOLO** | Local | Free | Custom model trained on plate region |

**Integration path:**

```
Frigate detects vehicle in driveway zone
    │
    ▼
Frigate publishes snapshot to MQTT
    │
    ▼
LPR service subscribes to vehicle snapshots
    │
    ▼
CodeProject.AI ALPR processes image
    │
    ▼
Plate + confidence published to MQTT:
homeios/cameras/driveway/lpr
{
  "plate": "ABC 1234",
  "confidence": 0.94,
  "region": "AE-DU",
  "timestamp": "2026-07-17T18:32:07Z",
  "camera": "driveway"
}
    │
    ▼
Context Engine: compare vs. known vehicles database
    │
    ├── Known vehicle (Sadiq's car) → open gate, notify arrival
    └── Unknown vehicle → log + alert if loitering > 5 minutes
```

### 9.4 Plate Database Schema

```sql
CREATE TABLE vehicles (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    person_id       UUID REFERENCES persons(id),
    plate_number    VARCHAR(20) NOT NULL,
    plate_region    VARCHAR(10),          -- 'AE-DU', 'UK', 'US-CA'
    make            VARCHAR(50),
    model           VARCHAR(50),
    color           VARCHAR(30),
    access_level    VARCHAR(20) DEFAULT 'resident', -- 'resident','visitor','blocked'
    notes           TEXT,
    created_at      TIMESTAMPTZ DEFAULT now()
);

CREATE TABLE lpr_events (
    id              UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    vehicle_id      UUID REFERENCES vehicles(id),
    plate_detected  VARCHAR(20) NOT NULL,
    confidence      FLOAT,
    camera_id       UUID REFERENCES cameras(id),
    event_type      VARCHAR(20),    -- 'arrival', 'departure', 'loitering'
    snapshot_path   TEXT,
    detected_at     TIMESTAMPTZ DEFAULT now()
);
```

---

## 10. Activity Understanding

### 10.1 Beyond Object Detection

Knowing that "a person is in the kitchen" is useful. Knowing that "a person is cooking at the stove" is actionable intelligence that drives:
- Auto-activating the kitchen exhaust fan
- Adjusting kitchen lighting to cooking-optimized level
- Monitoring for cooking safety (unattended stove)
- Suggesting recipe assistance through the conversation agent

Activity understanding is harder than object detection — it requires understanding **pose**, **context**, and **temporal sequence**.

### 10.2 Pose Estimation

Pose estimation detects body keypoints (17–133 keypoints depending on model) enabling activity classification:

| Pose/Keypoints | Detectable Activity |
|----------------|-------------------|
| Arms raised above shoulders | Reaching for something (cooking, shelves) |
| Seated posture (hip angle < 45°) | Sitting |
| Prone position (horizontal) | Lying down / sleeping |
| Arms close to body, standing | Standing / waiting |
| Rapid limb movement | Active movement / exercise |
| Fall detection (sudden horizontal transition) | Potential fall |

**Model options:**

| Model | Keypoints | Speed | Cost |
|-------|-----------|-------|------|
| **YOLOv8-pose** | 17 COCO keypoints | ~8ms/frame (GPU) | Free |
| **MediaPipe Pose** | 33 keypoints | ~15ms (CPU-only) | Free |
| **OpenPifPaf** | 17–133 keypoints | ~30ms (GPU) | Free |
| **ViTPose** | 17 keypoints | ~20ms (GPU) | Research, free |

### 10.3 Activity Classification Model

A lightweight temporal model processes sequences of pose keypoints to classify activities:

```python
# Activity classifier (pseudo-code — LSTM-based)

class ActivityClassifier:
    ACTIVITIES = [
        "standing", "sitting", "lying_down", "walking",
        "cooking", "exercising", "reading", "using_phone",
        "carrying_item", "potential_fall", "sleeping"
    ]

    def classify(self, pose_sequence: List[PoseKeypoints]) -> ActivityResult:
        """
        Classify activity from sequence of 30 pose frames (6 seconds at 5fps).
        """
        features = self.extract_features(pose_sequence)
        # Features: joint angles, velocity of keypoints, relative positions
        
        logits = self.lstm_model(features)
        activity = self.ACTIVITIES[torch.argmax(logits)]
        confidence = torch.softmax(logits, dim=-1).max().item()
        
        return ActivityResult(
            activity=activity,
            confidence=confidence,
            duration_seconds=len(pose_sequence) / 5
        )
```

### 10.4 Fall Detection

Fall detection is a safety-critical use case — particularly for elderly occupants or young children:

```
Fall detection pipeline:

Frame t:   Person standing (vertical aspect ratio > 1.5)
Frame t+1: Person moving rapidly
Frame t+2: Person horizontal (vertical aspect ratio < 0.5)
              + mmWave confirms person still present (not exited room)
              + No motion for 30+ seconds after fall

→ FALL DETECTED

AI Response:
  1. Alert notification to all registered phones (immediate)
  2. Two-way audio activated to room (ask "Are you okay?")
  3. If no response in 60 seconds → contact emergency contacts
  4. If no response in 120 seconds → optional: call emergency services
```

---

## 11. Pet Detection

### 11.1 Purpose

Pet detection separates the motion caused by pets from human motion — preventing false presence alerts and security false alarms.

YOLO natively detects: `dog`, `cat`, `bird` — these are standard COCO classes.

### 11.2 Pet Tracking

Beyond just detection, AI Home OS tracks individual pets by combining:
- YOLO bounding box tracking (ByteTrack multi-object tracker in Frigate)
- Fur color and pattern classification (custom lightweight classifier)
- Size estimation

This answers: "Where is the dog right now?" — a common household question and safety concern (did the dog get out of the yard?).

```
Pet location events:

homeios/cameras/backyard/events:
{
  "object": "dog",
  "pet_id": "max",        // matched by visual classifier
  "zone": "garden_zone",
  "confidence": 0.89,
  "action": "entered"
}

homeios/identity/pets/max/location:
{
  "location": "backyard",
  "last_seen": "2026-07-17T15:22:07Z",
  "confidence": 0.89
}
```

### 11.3 Pet Safety Alerts

| Scenario | Detection | Alert |
|----------|-----------|-------|
| Pet exits property | Zone exit event (yard boundary zone) | Immediate alert to owner |
| Pet near pool unsupervised | Pet detected in pool zone, no human present | Alert |
| Pet near road | Pet detected in driveway/gate zone | Alert |
| Pet stationary for unusual duration | No movement for >2 hours (unusual for active pet) | Welfare check alert |

---

## 12. Package Detection

### 12.1 Detection Approach

Packages are not a native COCO class in standard YOLO models. Detection requires one of:

1. **Fine-tuned YOLO model**: Train on package/delivery dataset (Roboflow "Package Detection" dataset — 5,000+ annotated images)
2. **Proxy detection**: Detect "person approaching + person departing + object remains" as delivery signature
3. **YOLO suitcase class**: Standard COCO includes "suitcase" which overlaps well with box/package shapes

**Recommended approach:** Fine-tuned YOLOv8 on a package detection dataset, deployed as a secondary inference model triggered only by front door zone events.

### 12.2 Delivery Event Logic

```
Delivery Detection Pipeline:

1. Motion detected at front door zone
2. YOLO: person detected (delivery person)
3. Person tracked: approaches, pauses, sets down object, departs
4. After person exits: check if new stationary object remains in doorstep zone
5. Object classification: package/box detected
6. Event: "Package delivered at [timestamp]"
7. Notification: push to user phone with snapshot

Theft Detection:
1. Package present in doorstep zone (confirmed delivery)
2. New person detected at doorstep
3. Person takes package and departs
4. Package zone: now empty
5. Check: is person a known resident?
   - No → "Package potentially taken by unknown person — alert"
   - Yes → "Package collected by [name]"
```

---

## 13. Intrusion Detection

### 13.1 Zone-Based Intrusion

Intrusion detection uses Frigate zones to define areas where persons should never be present at certain times:

```yaml
# Frigate zone config — intrusion zones
cameras:
  backyard:
    zones:
      pool_area:
        coordinates: "100,400,540,400,540,200,100,200"
        objects:
          - person
      perimeter_east:
        coordinates: "540,480,640,480,640,0,540,0"
        objects:
          - person

# These zones feed into AI Home OS rules:
# IF person enters pool_area AND time is 23:00–06:00 AND no known occupant → intrusion alert
# IF person enters perimeter_east AND no arrival event in last 5 minutes → possible intruder
```

### 13.2 Intelligent False Positive Reduction

Naive intrusion detection generates excessive false alarms. AI Home OS reduces these through:

| False Positive Cause | Mitigation |
|---------------------|-----------|
| Shadow / lighting change | Object detection confidence threshold > 0.65 |
| Neighbor visible through fence | Zone polygon excludes areas beyond property boundary |
| Expected occupant late arrival | Identity confirmation before alerting |
| Known visitor | Visitor registered in system — suppress alert |
| Delivery window (expected delivery) | Calendar event "expecting delivery" → suppress package carrier alert |
| Animal triggering person zone | Pet tracking active; filter animal bboxes from person zones |

### 13.3 Intrusion Alert Escalation

```mermaid
flowchart TD
    TRIGGER["Person detected in restricted zone\nor zone during restricted hours"]
    IDENTITY{Identity\nconfirmed?}
    KNOWN{Known person\n(resident or visitor)?}
    SUPPRESS["Suppress alert\nLog silently"]
    LOWCONF["Low confidence detection\n< 0.65"]
    DISMISS["Dismiss — log only"]
    ALERT1["Level 1 Alert:\nNotification + snapshot\n'Motion in backyard'"]
    RESPONSE1{User\nresponse\nwithin 60s?}
    ALERT2["Level 2 Alert:\nEmergency contacts notified\nSiren option offered"]
    RESPONSE2{User\nresponse\nwithin 120s?}
    ALERT3["Level 3:\nOptional auto-call\nemergency services\n(if configured)"]

    TRIGGER --> IDENTITY
    IDENTITY -->|"High confidence"| KNOWN
    IDENTITY -->|"Low confidence"| ALERT1
    KNOWN -->|"Yes"| SUPPRESS
    KNOWN -->|"No"| ALERT1
    LOWCONF --> DISMISS
    ALERT1 --> RESPONSE1
    RESPONSE1 -->|"Dismissed"| SUPPRESS
    RESPONSE1 -->|"No response"| ALERT2
    ALERT2 --> RESPONSE2
    RESPONSE2 -->|"Dismissed"| SUPPRESS
    RESPONSE2 -->|"No response"| ALERT3
```

---

## 14. Video Summarization

### 14.1 Daily Event Digest

Rather than requiring users to review hours of footage, AI Home OS generates a **daily visual digest**:

```
Daily Vision Summary — 17 July 2026

Morning (06:00–12:00):
  • 07:12 — Sadiq departed via front door (vehicle: Toyota Land Cruiser ABC-1234)
  • 08:45 — Delivery: 1 package at front door (collected by Sadiq at 19:03)
  • 10:20 — Cleaning staff arrived (known visitor: Maria)
  • 11:55 — Maria departed

Afternoon (12:00–18:00):
  • 14:30 — Unknown person approached front gate, pressed intercom, departed
  • 16:45 — Ahmed arrived (known visitor)

Evening (18:00–23:00):
  • 19:03 — Sadiq arrived home
  • 19:03 — Package collected by Sadiq
  • 21:30 — Max (dog) detected in garden, returned inside at 21:45

Night (23:00–06:00):
  • No significant events
```

### 14.2 LLM-Powered Summary Generation

The vision event log from the day is passed to the LLM (Ollama Llama 3.3) with a structured prompt to generate natural language summaries:

```python
# Video summarization prompt (pseudo-code)

def generate_daily_summary(events: List[VisionEvent]) -> str:
    events_json = json.dumps([e.to_dict() for e in events])
    
    prompt = f"""
You are generating the daily vision summary for an AI home system.
Below are all vision events detected today. Summarize them naturally
and concisely, grouped by time period. Highlight anything unusual.
Do not mention events that are routine (e.g., residents arriving/departing at normal times).
Flag: unknown persons, security events, package deliveries, safety events.

Events:
{events_json}

Generate a structured daily summary:
"""
    return ollama.generate(model="llama3.3:8b", prompt=prompt)
```

---

## 15. Privacy Architecture

### 15.1 Privacy Masking

Frigate supports **static privacy masks** — regions of the camera frame that are permanently blurred or blacked out before any processing or recording occurs. These masks are applied in FFmpeg before the stream reaches YOLO.

**Mandatory privacy masks:**
- Any portion of a neighbor's property visible in camera field of view
- Public sidewalk beyond property boundary (jurisdiction-dependent)
- Bedroom windows of the home visible from outdoor cameras

```yaml
# Frigate privacy mask config
cameras:
  front_door:
    ffmpeg:
      output_args:
        record: -f segment -segment_time 10 -segment_format mp4
    motion:
      mask:
        # Static privacy mask: neighbor's driveway on left edge
        - "0,0,120,0,120,480,0,480"
```

### 15.2 Dynamic Privacy Mode

Occupants can activate **privacy mode** via voice command, wall panel, or mobile app:

```
"JARVIS, privacy mode on"
  → All indoor cameras: stop detection + recording
  → Outdoor cameras: continue security monitoring (reduced)
  → Facial recognition: suspended
  → Duration: until explicitly deactivated or location-based trigger

"JARVIS, privacy mode — bedroom only"
  → Bedroom camera (if installed): detection + recording paused
  → All other cameras: normal operation
```

### 15.3 Guest Privacy

When a guest arrives and is registered as a temporary visitor:
- Their face is **not** enrolled in the permanent face database
- They appear as "Guest — [name]" in event logs
- All footage containing the guest is **automatically deleted** within 48 hours of their departure
- Guests can request immediate deletion of their footage via QR code on wall panel

### 15.4 Data Encryption

| Data Type | Encryption | Location |
|-----------|-----------|---------|
| Live stream (LAN) | None (trusted VLAN) | Internal VLAN 40 only |
| Live stream (remote/VPN) | WireGuard AES-256-GCM | VPN tunnel |
| Recorded footage (NVMe) | LUKS volume encryption | AI server NVMe |
| Recorded footage (NAS) | Synology Shared Folder encryption (AES-256) | NAS |
| Face embeddings (database) | PostgreSQL column-level encryption (pgcrypto) | DB server |
| Snapshots on disk | Filesystem encryption (LUKS) | AI server |
| Cloud backup | AES-256 client-side before upload | Backblaze B2 |

### 15.5 Legal Considerations

> **This section is informational only. Consult legal counsel for your jurisdiction before deploying cameras.**

| Jurisdiction | Key Requirement |
|-------------|----------------|
| EU (GDPR) | Facial recognition of visitors requires consent; data minimization; retention limits |
| UK (DPA 2018) | Must display signage if cameras cover public areas |
| UAE | Federal law limits residential camera coverage to private property only |
| US (varies by state) | Generally permissible on own property; audio recording may require consent |

**AI Home OS default posture:** Minimize data collection, maximize privacy, never record audio through cameras (use dedicated audio system with explicit consent), provide easy deletion for any person's data.

---

## 16. Recording Policies & Retention

### 16.1 Recording Modes

| Mode | Storage Rate | Use Case |
|------|-------------|---------|
| **Continuous low-res** | ~5 GB/camera/day (360p, H.265) | 24-hour rolling buffer for incident review |
| **Event clips** | ~50–200 MB/event | 30–60 second clips around each detection |
| **Snapshots** | ~100 KB/snapshot | Thumbnail for each event |
| **Manual recording** | User-initiated | Full-resolution, indefinite retention |

### 16.2 Retention Policy

```yaml
# Frigate retention policy (reference)
record:
  retain:
    days: 3               # All recordings: 3 days
    mode: motion          # Only retain segments with motion
  events:
    retain:
      default: 30         # Default: 30 days
      objects:
        person:           60    # Person events: 60 days
        car:              30    # Vehicle events: 30 days
        package:          90    # Package events: 90 days (delivery proof)
        # Safety events are retained indefinitely by the Safety Agent override
```

### 16.3 Storage Calculator

For an 8-camera system:

| Type | Volume/Day | 30-Day Total |
|------|-----------|-------------|
| Continuous low-res (8 cameras) | ~40 GB | ~1.2 TB |
| Event clips (avg 30 events/camera/day) | ~8 GB | ~240 GB |
| Snapshots | ~0.5 GB | ~15 GB |
| **Total** | **~48.5 GB/day** | **~1.45 TB/month** |

NAS recommendation: 8 TB usable minimum for 30-day retention; 16 TB usable for 60-day retention.

---

## 17. Storage Architecture

```mermaid
graph LR
    FRIGATE["Frigate NVR\nAI Server"]

    subgraph HOT["Hot Storage — NVMe (1 TB)"]
        FRDB["Frigate SQLite DB\nEvent index"]
        CLIPS24["24hr rolling buffer\n~50 GB"]
        RECENT["Recent event clips\n7 days\n~350 GB"]
    end

    subgraph WARM["Warm Storage — NAS HDD (8 TB+)"]
        ARCHIVE["Event archive\n8–60 days\n~1.5 TB"]
        SNAPS["Snapshots archive\n~15 GB/month"]
        FACEDB["Face enrollment photos\n(encrypted)\n~1 GB"]
    end

    subgraph COLD["Cold Storage — Cloud (encrypted)"]
        SECURITY["Security events\n(intrusion, packages)\nIndefinite"]
        BACKUP["Database backup\n90 days"]
    end

    FRIGATE --> HOT
    HOT -->|"Daily move: events >7 days"| WARM
    WARM -->|"Weekly: security events only"| COLD
```

---

## 18. AI Inference Architecture

### 18.1 Inference Service Architecture

```mermaid
flowchart LR
    subgraph INFERENCE["AI Inference Services (Docker)"]
        FRIGATE_SVC["Frigate\nYOLO Detection\nMotion\nTracking\nRecording"]
        FR_SVC["Face Recognition Service\nDeepFace + ArcFace\nPort 8080"]
        LPR_SVC["LPR Service\nCodeProject.AI\nPort 32168"]
        ACTIVITY_SVC["Activity Service\nYOLOv8-pose +\nLSTM Classifier\nPort 8081"]
        VSUMM_SVC["Video Summary Service\nEvent aggregator +\nOllama LLM\nPort 8082"]
    end

    MQTT["MQTT Broker"]
    FRIGATE_SVC -->|"Detection events"| MQTT
    FRIGATE_SVC -->|"Snapshot path"| FR_SVC
    FRIGATE_SVC -->|"Vehicle snapshot"| LPR_SVC
    FRIGATE_SVC -->|"Person crop stream"| ACTIVITY_SVC
    MQTT -->|"Daily trigger"| VSUMM_SVC

    FR_SVC -->|"Identity event"| MQTT
    LPR_SVC -->|"Plate event"| MQTT
    ACTIVITY_SVC -->|"Activity event"| MQTT
    VSUMM_SVC -->|"Daily summary"| MQTT
```

### 18.2 GPU Memory Management

With multiple inference workloads sharing the RTX 4070's 12 GB VRAM, careful memory management is required:

```python
# GPU memory allocation strategy (pseudo-code)

GPU_MEMORY_BUDGET = 12 * 1024  # 12 GB in MB

ALLOCATIONS = {
    "frigate_yolo":     2048,   # YOLOv10-M TensorRT
    "face_recognition": 1024,   # ArcFace ResNet100
    "lpr":              512,    # OpenALPR
    "activity_pose":    1024,   # YOLOv8-pose
    "ollama_8b":        8192,   # Llama 3.3 8B Q4
    # Total: 12,800 MB → exceeds 12 GB
}

# Solution: Dynamic loading — unload LLM from GPU when vision inference peaks
# Frigate + face recognition + LPR: ~3.5 GB
# LLM: 8.2 GB
# Total when both active: 11.7 GB → fits if no other overhead

# Priority: Vision inference > LLM (LLM can tolerate 200ms extra latency)
# When vision GPU demand spikes: LLM falls back to RAM (slower)
```

---

## 19. Vision Event Pipeline

### 19.1 MQTT Event Schema

All vision events are published to the MQTT broker under the `homeios/cameras/` namespace:

```json
// Detection event
{
  "topic": "homeios/cameras/front_door/events/detection",
  "payload": {
    "event_id": "evt_01J5KQ2MXYZ123",
    "camera": "front_door",
    "timestamp": "2026-07-17T18:32:07.441Z",
    "type": "new",
    "object": {
      "label": "person",
      "score": 0.91,
      "box": {"xmin": 280, "ymin": 120, "xmax": 450, "ymax": 478},
      "area": 42840,
      "ratio": 2.14,
      "region": {"xmin": 200, "ymin": 80, "xmax": 550, "ymax": 480}
    },
    "zones": ["doorstep"],
    "snapshot_path": "/media/clips/front_door/2026-07-17/evt_01J5KQ2MXYZ123.jpg",
    "has_clip": false
  }
}

// Identity enrichment event (published after face recognition completes)
{
  "topic": "homeios/cameras/front_door/events/identity",
  "payload": {
    "event_id": "evt_01J5KQ2MXYZ123",
    "camera": "front_door",
    "timestamp": "2026-07-17T18:32:07.921Z",
    "identity": {
      "person_id": "sadiq",
      "display_name": "Sadiq",
      "confidence": 0.89,
      "certainty": "high"
    },
    "action": "arrived_home"
  }
}

// LPR event
{
  "topic": "homeios/cameras/driveway/events/lpr",
  "payload": {
    "camera": "driveway",
    "timestamp": "2026-07-17T18:31:52.110Z",
    "plate": "ABC 1234",
    "confidence": 0.96,
    "vehicle_id": "sadiq_car",
    "vehicle_owner": "sadiq",
    "action": "approaching"
  }
}
```

### 19.2 Event Processing in Context Engine

```python
# Context Engine vision event handler (pseudo-code)

class VisionEventHandler:
    def on_identity_event(self, event: IdentityEvent):
        person = self.identity_service.get_person(event.person_id)
        
        # Update person location
        self.context_store.update_person_location(
            person_id=event.person_id,
            location=f"camera:{event.camera}",
            confidence=event.confidence,
            source="vision"
        )
        
        # Determine semantic action
        prev_location = self.context_store.get_previous_location(event.person_id)
        
        if prev_location == "away" and event.camera in EXTERIOR_CAMERAS:
            self.emit_situation("person_arrived_home", person=person)
        elif prev_location == "home" and event.camera in EXTERIOR_CAMERAS:
            self.emit_situation("person_departed_home", person=person)
```

---

## 20. Integration with AI Home OS

### 20.1 Vision → AI Agent Events

| Vision Event | Receiving Agent | AI Action |
|-------------|----------------|-----------|
| Known person arrives at front door | Conversation Agent | "Welcome home, [name]. [Personalized greeting]" |
| Unknown person at front door | Security Agent | Alert + snapshot to user |
| Package delivered | Conversation Agent | "Your package has arrived" |
| Package theft | Security Agent | Urgent alert + recording |
| Intrusion in restricted zone | Security Agent | Escalating alert |
| Cooking activity detected | Energy Agent | Pre-activate exhaust fan |
| Potential fall detected | Health Agent | Alert + two-way audio |
| Person at front door (button pressed) | Conversation Agent | Two-way intercom via wall panel + phone |
| Known vehicle detected | Energy Agent | Prepare home for arrival (HVAC, lights) |
| Delivery person departing | Conversation Agent | Queue package arrival notification |

### 20.2 AI Agent → Vision Commands

| Agent | Command | Action |
|-------|---------|--------|
| Security Agent | `cameras/front_door/ptz/preset/gate` | Move PTZ camera to gate preset |
| Security Agent | `cameras/all/recording/manual/start` | Force all cameras to record |
| Conversation Agent | `cameras/front_door/snapshot` | Capture snapshot for user preview |
| Security Agent | `cameras/backyard/motion_alerts/disable` | Suppress alerts during known garden party |
| Planning Agent | `cameras/system/privacy_mode/on` | Disable indoor cameras (privacy mode) |

---

## 21. Camera BOM

### 21.1 Reference 4-Bedroom Home — Camera BOM

| Item | Model | Qty | Unit $ | Total $ |
|------|-------|-----|--------|---------|
| Front door camera (doorbell) | Reolink Video Doorbell PoE | 1 | $70 | $70 |
| Driveway camera (8MP LPR) | Reolink RLC-810A | 1 | $55 | $55 |
| Front exterior wide | Amcrest IP8M-2483EW | 1 | $100 | $100 |
| Backyard camera | Reolink RLC-810A | 1 | $55 | $55 |
| Side gate | Reolink RLC-823A | 1 | $80 | $80 |
| Garage exterior | Reolink RLC-810A | 1 | $55 | $55 |
| Garage interior (fisheye) | Reolink FE-W | 1 | $70 | $70 |
| Living room (optional indoor) | Reolink E1 Zoom | 1 | $45 | $45 |
| **Camera hardware subtotal** | | **8** | | **$530** |

| Item | Notes | Cost |
|------|-------|------|
| PoE switch (cameras) | Already in Ch.1 BOM | $0 (shared) |
| Cat6A cabling | Already in Ch.1 BOM | $0 (shared) |
| CodeProject.AI (LPR) | Open source | $0 |
| Plate Recognizer (optional) | $5/month for higher accuracy | $60/year |
| Frigate NVR | Open source | $0 |
| NAS storage for recordings | Already in Ch.1 BOM (shared) | $0 (shared) |

**Camera Layer Total: ~$530 hardware + optional $60/year LPR service**

---

## 22. Design Decisions & Trade-offs

### 22.1 Frigate vs. Commercial NVR (Blue Iris, Milestone, Genetec)

| Factor | Frigate | Blue Iris | Milestone / Genetec |
|--------|---------|-----------|---------------------|
| Cost | Free (open source) | $70 one-time | $$$$ per channel/year |
| AI integration (MQTT) | Native | Plugin | API |
| YOLO support | Native (best-in-class) | Plugin | API integration |
| Home Assistant integration | Native | Via plugin | None |
| Privacy (local-only) | ★★★★★ | ★★★★★ | ★★★ |
| Reliability | ★★★★ | ★★★★★ | ★★★★★ |
| Enterprise features | ★★ | ★★★ | ★★★★★ |

**Decision:** Frigate for residential and small commercial. For enterprise (hospital, campus), Milestone XProtect with REST API bridge to AI Home OS.

### 22.2 Dedicated LPR Camera vs. Dual-Purpose Camera

| Approach | Pros | Cons |
|----------|------|------|
| Separate LPR camera (narrower FOV, 8mm lens) | Optimal for plate capture at distance | Extra cost, extra cable run |
| Dual-purpose camera (4mm lens, 8MP) | One camera does both | Compromises on both tasks |

**Decision:** For properties with a gate at >8m from the road: separate LPR camera. For shorter driveways (<8m): single 8MP camera at 4mm lens captures both scene context and sufficient plate resolution.

### 22.3 On-Camera AI (Edge) vs. Centralized GPU Inference

| Approach | Pros | Cons |
|----------|------|------|
| Centralized GPU (Frigate on AI server) | Best GPU available, centralized management | Single point of failure |
| Per-camera AI chip (e.g., Ambarella H22) | No network bandwidth for inference, resilient | Very limited model capability, expensive cameras |
| Hybrid (Coral per camera + GPU for complex tasks) | Balanced | Complex deployment |

**Decision:** Centralized GPU inference (AI server) for v1. Per-camera AI chips (v3 roadmap) for edge resilience in commercial deployments.

---

## 23. Failure Modes & Redundancy

| Failure | Impact | Detection | Recovery |
|---------|--------|-----------|---------|
| AI server crash | No detection, recording continues in Frigate | MQTT Last Will | Cameras continue recording independently; detection resumes on restart |
| GPU failure | No AI detection | Frigate GPU error log | Fall back to Coral TPU (if installed) or CPU inference (slow) |
| Camera offline (PoE failure) | Blind spot on that camera | MQTT "offline" event | Alert Maintenance Agent; other cameras continue |
| NAS offline | Cannot write archives | NAS ping failure | Continue to NVMe (limited space); alert user |
| Frigate crash | No detection or recording | Docker restart policy | Auto-restart; cameras buffer in their own memory |
| RTSP stream dropout | Camera missing from Frigate | Frigate connection error | Frigate auto-reconnects every 30 seconds |
| Face DB corruption | No recognition | All faces return "unknown" | Restore from NAS backup; pgvector index rebuild |

---

## 24. Risks

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| False positive intrusion alert erodes user trust | High (early deployment) | High | Tune confidence thresholds; multi-stage confirmation |
| Facial recognition misidentifies person | Low (with good enrollment) | Medium | Medium confidence triggers notification not action; high confidence for automatic actions only |
| Camera footage subpoenaed by authorities | Low | High | Transparent retention policy; encrypted storage; deletion on schedule |
| Adversarial attack on face recognition (printed photo) | Very low | Medium | Liveness detection (3D face check via depth sensor or IR) — v2 feature |
| Privacy violation: neighbor captured on recording | Medium | High | Privacy masks on all cameras covering neighbor property; check FOV during installation |
| Storage fills up causing recording gaps | Medium | Medium | Monitoring alerts when NAS >80% capacity; automatic retention reduction |
| Camera firmware vulnerability | Low-Medium | High | Regular firmware updates; cameras isolated on VLAN 40 with no internet |

---

## 25. Future Improvements

| Improvement | Version | Description |
|-------------|---------|-------------|
| Liveness detection | v2 | Prevent face spoof attacks with depth camera or IR liveness check |
| Re-identification across cameras | v2 | Track a person as they move from one camera's view to another without facial recognition (embedding-based re-ID) |
| Anomaly detection via video foundation model | v2 | Use VideoMAE or similar model to detect unusual activities without specific labels |
| Per-camera edge inference (Hailo-8) | v3 | Deploy Hailo-8 NPU chip on camera (4 TOPS) for local object detection — no network bandwidth needed |
| Emotion detection | v3 | Detect emotional state for conversation agent context (distressed person at door) |
| Video foundation model integration | v3 | GPT-4o / Gemini 1.5 vision API for complex scene understanding (with privacy-safe preprocessing) |
| 3D scene reconstruction | v3 | Build a 3D model of the home from camera feeds for spatial AI reasoning |
| Multi-camera activity tracking | v2 | Seamlessly track a person's path through the home across all cameras |
| Thermal camera integration | v2 | FLIR or Seek thermal cameras for night intrusion detection without visible light |

---

## 26. References

1. **Frigate NVR Documentation** — https://docs.frigate.video/
2. **go2rtc Streaming** — https://github.com/AlexxIT/go2rtc
3. **YOLOv10 Paper** — Wang et al., 2024 — https://arxiv.org/abs/2405.14458
4. **ArcFace Paper** — Deng et al., 2019 — https://arxiv.org/abs/1801.07698
5. **DeepFace Framework** — https://github.com/serengil/deepface
6. **RetinaFace** — Deng et al., 2019 — https://arxiv.org/abs/1905.00641
7. **ByteTrack Multi-Object Tracking** — Zhang et al., 2022 — https://arxiv.org/abs/2110.06864
8. **CodeProject.AI** — https://www.codeproject.com/Articles/5322557/CodeProject-AI-Server
9. **OpenALPR / Plate Recognizer** — https://platerecognizer.com/
10. **MediaPipe Pose** — https://developers.google.com/mediapipe/solutions/vision/pose_landmarker
11. **Roboflow Package Detection Dataset** — https://roboflow.com/
12. **pgvector** — https://github.com/pgvector/pgvector
13. **ONVIF Profile S Specification** — https://www.onvif.org/profiles/profile-s/
14. **RTSP RFC 2326** — https://datatracker.ietf.org/doc/html/rfc2326
15. **GDPR Art. 9 — Biometric Data** — https://gdpr-info.eu/art-9-gdpr/
16. **NIST Face Recognition Evaluation (FRVT)** — https://pages.nist.gov/frvt/html/frvt11.html
17. **TensorRT Optimization Guide** — https://docs.nvidia.com/deeplearning/tensorrt/
18. **H.265/HEVC Compression** — ITU-T H.265 — https://www.itu.int/rec/T-REC-H.265

---

*Previous: [Chapter 02 — Sensor Layer](Chapter-02.md)*  
*Next: [Chapter 04 — Audio System](Chapter-04.md)*

---

> **Document maintained by:** AI Home OS Architecture Team  
> **Last updated:** 2026-07-17  
> **Chapter status:** Draft v1.0 — Open for community review
