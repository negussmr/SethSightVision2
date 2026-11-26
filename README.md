# SethSightVision2

An ESP32-CAM live-video pipeline with real-time YOLO object detection, plus a fabric classifier (fine-tuned Vision Transformer) trained on Roboflow.

> **Author:** Justine Clarke Caguete
> **Also known as:** `negussmr` (GitHub) · `seth-caguete` (Roboflow)

---

## What's in this project

This research project has two parts:

| Part | What it does | Where it lives |
|---|---|---|
| **1. ESP32-CAM + YOLO system** | An ESP32-CAM streams MJPEG video over Wi-Fi; a Python server runs Ultralytics YOLO on the stream and shows boxes, confidence, FPS, recording, screenshots and CSV logs. | This repository |
| **2. Fabric classifier (ViT)** | Single-label image classification of fabric photos into 5 classes. | Trained on Roboflow (model ID below) |

> **Note:** The ViT fabric classifier is trained and hosted on Roboflow. The code in this repository does **not** yet run it; the server here loads Ultralytics YOLO weights (`models/best.pt`, or a stock model such as `yolov8n.pt`). See [Roadmap](#roadmap).

---

## Part 2: Fabric Classifier (ViT)

| | |
|---|---|
| **Roboflow model ID** | `seth-caguete/fabric-reference-library-5-vit-base-patch16-224-in21k-t2` |
| **Task** | Single-label image classification |
| **Classes (5)** | cotton, denim, linen, silk, wool |
| **Architecture** | Fine-tuned Vision Transformer, ViT-Base, 16×16 patches, 224×224 input |
| **Pre-training** | ImageNet-21k |
| **Dataset** | Fabric Reference Library, frozen version 5 |
| **Training run** | 2 (suffix `t2`) |
| **Status** | Finished and deployable |
| **Reported accuracy** | 79.41% (the Roboflow training summary does not say which split this was measured on) |

### Dataset (version 5)

| Split | Images |
|---|---|
| Train | 2,600 |
| Validation | 68 |
| Test | 28 |
| **Total** | **2,696** |

- **Preprocessing:** auto-orientation, contrast adjustment, random sampling, resizing, tiling
- **Augmentations:** brightness, exposure, saturation, camera gain

### Please read before relying on the accuracy

- The 79.41% figure has no stated evaluation split.
- The validation set (68 images) and test set (28 images) are very small, so the accuracy estimate is uncertain: a few images change it by several percentage points.
- No per-class results are reported yet, so it is unknown which fabrics are confused with each other.

### Run the classifier

Get the exact, up-to-date code from your Roboflow project's **Deploy** tab. A typical pattern with Roboflow's `inference` package looks like this (check it against the Deploy tab; Roboflow may change its API):

```python
# pip install inference
from inference import get_model

model = get_model(
    model_id="<your-roboflow-model-id>",   # from the Deploy tab
    api_key="YOUR_ROBOFLOW_API_KEY",        # never commit this key
)
result = model.infer("path/to/fabric.jpg")
print(result)
```

> **Security:** Keep your Roboflow API key in an environment variable, never in the repository.

---

## Part 1: ESP32-CAM + YOLO system

```
SethSightVision2/
├── server.py          # Main entry point (reads ESP32 stream, runs YOLO, Flask dashboard)
├── config.py          # All settings (model path, ESP32 IP, thresholds, GPU, logging)
├── detection.py       # Ultralytics YOLO wrapper (GPU/CPU, FP16, error handling)
├── esp32_cam.ino      # ESP32-CAM firmware (MJPEG stream on port 81)
├── kay kerwin.py      # Standalone Flask server: POST /detect, /health, /webcam
├── kerwinHAHAHAH.cpp  # ESP32 firmware variant that talks to the Flask server
└── ESP32_YOLO.zip     # Full copy of the project incl. classes.py, utils.py, logger.py,
                       # recorder.py, requirements.txt
```

### Setup

Python 3.9–3.12 is required.

```bash
git clone https://github.com/negussmr/SethSightVision2.git
cd SethSightVision2
unzip ESP32_YOLO.zip && cd ESP32_YOLO     # supporting files are inside the zip
python -m venv venv
source venv/bin/activate                  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```

1. Open `esp32_cam/esp32_cam.ino` in the Arduino IDE, select **AI Thinker ESP32-CAM**, and set your Wi-Fi name and password.
2. Upload, open the Serial Monitor at 115200 baud, and note the IP address it prints.
3. Put that IP in `config.py` (`ESP32_IP`).
4. Put your YOLO weights in `models/` and set `MODEL_PATH` in `config.py` (or use `yolov8n.pt`).
5. Run `python server.py`.

Keyboard controls: **Q/ESC** quit · **S** screenshot · **R** record · **F** fullscreen · **C** confidence · **B** boxes · **P** pause · **SPACE** resume.

Browser view: `http://<your-pc-ip>:5000/stream`, status at `/status`.

See [DOCUMENTATION.md](DOCUMENTATION.md) for details and troubleshooting.

---

## Roadmap

- [ ] Run the Roboflow ViT fabric classifier from the server (for example, classify a captured frame)
- [ ] Report accuracy on the test split with a confusion matrix
- [ ] Clean up repository layout (see DOCUMENTATION.md, section 9)

## How to Cite

GitHub shows a **"Cite this repository"** button from [`CITATION.cff`](CITATION.cff).

**APA**

> Caguete, J. C. (2026). *SethSightVision2: ESP32-CAM live detection and ViT fabric classification* [Computer software]. GitHub. https://github.com/negussmr/SethSightVision2

**BibTeX**

```bibtex
@software{caguete2026sethsightvision2,
  author = {Caguete, Justine Clarke},
  title  = {SethSightVision2: ESP32-CAM Live Detection and ViT Fabric Classification},
  year   = {2026},
  url    = {https://github.com/negussmr/SethSightVision2}
}
```

> **Note on names:** The author's name is **Justine Clarke Caguete**. `negussmr` (GitHub) and `seth-caguete` (Roboflow) are online handles for the same person. Please cite using the author name above.

## Acknowledgements

- Dosovitskiy, A., et al. (2021). *An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale.* ICLR. https://arxiv.org/abs/2010.11929
- [Roboflow](https://roboflow.com) for dataset management and model training
- [Ultralytics YOLO](https://github.com/ultralytics/ultralytics) and [OpenCV](https://opencv.org)

## License

[FILL IN: e.g. MIT. Add a LICENSE file to the repository.]

## Contact

Justine Clarke Caguete: [FILL IN: email or ORCID]
