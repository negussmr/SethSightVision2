# SethSightVision2: Technical Documentation

Author: Justine Clarke Caguete (`negussmr` on GitHub, `seth-caguete` on Roboflow)

## 1. Overview

The project has two parts that are currently separate:

1. **ESP32-CAM + YOLO system** (this repository): live video from an ESP32-CAM, processed by a Python server running Ultralytics YOLO.
2. **Fabric classifier** (Roboflow): a fine-tuned ViT-Base (patch 16, 224×224, ImageNet-21k pre-trained) that labels a fabric photo as cotton, denim, linen, silk, or wool.

## 2. System Architecture (Part 1)

```
ESP32-CAM  --MJPEG over Wi-Fi (port 81)-->  server.py (PC)
                                              |-- detection.py (YOLO, GPU/CPU)
                                              |-- OpenCV window + overlays
                                              |-- recorder.py / logger.py (video, CSV)
                                              '-- Flask dashboard (port 5000)
```

There is also a second, simpler flow: `kerwinHAHAHAH.cpp` (ESP32 firmware that talks to a Python server) and `kay kerwin.py` (Flask server with `POST /detect`, `GET /health`, `GET /webcam`, using `yolov8n.pt`).

### Key settings (`config.py`)

| Setting | Default | Meaning |
|---|---|---|
| `MODEL_PATH` | `models/best.pt` | YOLO weights file (or a stock name like `yolov8n.pt`) |
| `ESP32_IP` | `192.168.1.50` | IP printed by the ESP32 serial monitor |
| `CONFIDENCE_THRESHOLD` | 0.45 | Minimum detection confidence |
| `IOU_THRESHOLD` | 0.45 | NMS overlap threshold |
| `IMAGE_SIZE` | 640 | Inference resolution |
| `GPU_ENABLED` / `USE_HALF_PRECISION` | True | Use CUDA and FP16 when available |
| `DETECTION_EVERY_N_FRAMES` | 1 | Detect every Nth frame |
| `ENABLE_FLASK_SERVER` / `FLASK_PORT` | True / 5000 | Browser dashboard |

## 3. Setup

Requires Python 3.9–3.12.

```bash
git clone https://github.com/negussmr/SethSightVision2.git
cd SethSightVision2
unzip ESP32_YOLO.zip && cd ESP32_YOLO
python -m venv venv
source venv/bin/activate        # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

### ESP32-CAM firmware

1. Arduino IDE: install the **esp32** board package, select **AI Thinker ESP32-CAM**, Partition Scheme **Huge APP (3MB No OTA/1MB SPIFFS)**.
2. Set `WIFI_SSID` and `WIFI_PASSWORD` in `esp32_cam.ino`. **Do not commit your real Wi-Fi details.**
3. Hold **IO0** while uploading, then release.
4. Serial Monitor at 115200 baud shows the stream URL, `http://<ip>:81/stream`.
5. Put the IP in `config.py`, then run `python server.py`.

## 4. The Fabric Classifier (Part 2)

### Dataset: Fabric Reference Library, version 5

| Item | Value |
|---|---|
| Classes | cotton, denim, linen, silk, wool |
| Total images | 2,696 |
| Train / Validation / Test | 2,600 / 68 / 28 |
| Preprocessing | auto-orientation, contrast adjustment, random sampling, resizing, tiling |
| Augmentations | brightness, exposure, saturation, camera gain |
| Images per class | [FILL IN] |
| Image source and capture setup | [FILL IN] |

### Model

| Item | Value |
|---|---|
| Model ID | `seth-caguete/fabric-reference-library-5-vit-base-patch16-224-in21k-t2` |
| Architecture | ViT-Base, 16×16 patches, 224×224 input |
| Pre-training | ImageNet-21k |
| Platform | Roboflow (training run 2) |
| Reported accuracy | 79.41% (split not specified) |
| Learning rate, batch size, epochs | [FILL IN if shown in Roboflow] |

### Notes on interpreting the results

- Roboflow's summary does not say whether 79.41% is validation or test accuracy. Check the training graphs or the model's evaluation page in Roboflow and write the answer here.
- The validation and test sets are tiny (68 and 28 images). One test image is about 3.6 percentage points. Report this uncertainty in any paper, and consider rebalancing the splits (for example 70/15/15) in a future dataset version.
- Report per-class precision, recall and a confusion matrix, since accuracy alone hides which fabrics get confused (linen vs. cotton is a likely pair, but this is untested).

### Using it

Use the snippet from the Roboflow **Deploy** tab. Store your API key in an environment variable:

```bash
export ROBOFLOW_API_KEY="your-key-here"
```

## 5. Running the YOLO server

```bash
python server.py
```

| Key | Action |
|---|---|
| Q / ESC | Quit |
| S | Screenshot |
| R | Toggle recording |
| F | Fullscreen |
| C | Toggle confidence |
| B | Toggle boxes |
| P / SPACE | Pause / resume |

## 6. Troubleshooting

| Problem | Fix |
|---|---|
| `Model file not found` | Put your weights in `models/` and match `MODEL_PATH` in `config.py` |
| Stream never appears | Check the ESP32 is on the same network; open `http://<ip>:81/stream` in a browser |
| Disconnects / choppy video | Lower `CAMERA_FRAME_SIZE` (VGA), improve Wi-Fi signal |
| Low FPS | Lower `IMAGE_SIZE`, use GPU, or raise `DETECTION_EVERY_N_FRAMES` |
| `esp_camera_init failed 0x105` | Check wiring/power and that the AI Thinker board is selected |
| Brownout resets | Use a stable 5V/2A power supply |
| `ModuleNotFoundError` | Run `pip install -r requirements.txt` in the active virtual environment |

## 7. Reproducibility Checklist

- [ ] Which split the 79.41% was measured on
- [ ] Images per class
- [ ] Training hyperparameters (from Roboflow)
- [ ] Exact dataset version export or link
- [ ] Test-set confusion matrix
- [ ] Library versions (`requirements.txt`)
- [ ] LICENSE file




