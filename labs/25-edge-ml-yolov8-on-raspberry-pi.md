# Lab 25 — Edge ML: YOLOv8 on Raspberry Pi

**Goal**: Run real-time object detection on a Raspberry Pi camera. Benchmark
PyTorch vs ONNX vs ONNX INT8 and understand why each is different.

**Time box**: 8 hours

**Hardware**: Raspberry Pi 5 (4GB+) + Pi Camera v3

**Language**: Python

**Done when**:
- [ ] YOLOv8n running on laptop webcam first (baseline)
- [ ] Model exported to ONNX INT8 (quantized for ARM)
- [ ] Pi Camera v3 integrated with `picamera2`
- [ ] Detections published to MQTT: `vision/camera/detections`
- [ ] MJPEG stream accessible at `http://pi.local:8080/stream`
- [ ] FPS benchmarked across all three backends in a table in the README

**Key concepts**:
- YOLOv8 inference pipeline: preprocess → infer → NMS → draw
- ONNX as a framework-agnostic model format
- INT8 quantization: smaller model, faster inference, slight accuracy loss
- ARM NEON SIMD instructions (why ONNX Runtime is faster on ARM64)
- MJPEG streaming: the simplest way to stream annotated video over HTTP

**Testing requirement**: Unit tests for preprocessing and postprocessing functions. The inference itself is not unit-tested — test the code around it. **CI**: `ruff` → `pytest --cov` (≥70% coverage on preprocessing/postprocessing modules).

**AI usage note**: ONNX export and quantization have subtle failure modes. Use AI freely for debugging model conversion: *"My ONNX INT8 export is producing garbage output — what could cause this?"* The ML concepts (what quantization trades off, what NMS does) should be your own understanding.

---

## Phase 12 — ML Fundamentals

---
