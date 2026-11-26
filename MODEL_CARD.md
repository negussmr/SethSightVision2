# Model Card: Fabric Reference Library: ViT Fabric Classifier

**Roboflow model ID:** `seth-caguete/fabric-reference-library-5-vit-base-patch16-224-in21k-t2`
**Author:** Justine Clarke Caguete (`seth-caguete` on Roboflow, `negussmr` on GitHub)
**Code repository:** https://github.com/negussmr/SethSightVision2

## Model Details

| | |
|---|---|
| Task | Single-label image classification |
| Classes | cotton, denim, linen, silk, wool (5) |
| Architecture | Vision Transformer (ViT-Base), 16×16 patches |
| Input | 224×224 image |
| Pre-training | ImageNet-21k |
| Training platform | Roboflow |
| Dataset | Fabric Reference Library, frozen version 5 |
| Training run | 2 |
| Status | Finished and deployable |

## Intended Use

- Research on fabric-type recognition from photographs
- A baseline for comparing against other vision models

**Not intended for:** quality control, purchasing, or any decision where a wrong fabric label has real consequences. The model has not been validated for that.

## Training Data

Frozen dataset version 5, 2,696 images:

| Split | Images | Share |
|---|---|---|
| Train | 2,600 | 96.4% |
| Validation | 68 | 2.5% |
| Test | 28 | 1.0% |

- **Preprocessing:** auto-orientation, contrast adjustment, random sampling, resizing, tiling
- **Augmentations:** brightness, exposure, saturation, camera gain
- **Images per class:** [FILL IN]
- **Image source, camera, lighting, collection method:** [FILL IN]

## Evaluation

| Metric | Value | Split |
|---|---|---|
| Accuracy | 79.41% | Not specified in the Roboflow training summary |

Per-class precision/recall, confusion matrix, and test-split results: [FILL IN]

## Limitations

- **Small evaluation sets.** With 68 validation and 28 test images, the accuracy estimate is uncertain; each image moves it by roughly 1.5 to 3.5 percentage points depending on the split.
- **Unknown evaluation split.** It is unclear whether 79.41% refers to validation or test data.
- **Five classes only.** The model always picks one of the five classes, even for a fabric that is none of them (for example, polyester, nylon, or blends).
- **Data distribution.** Performance on photos with different lighting, cameras, backgrounds, or fabric colors than the training data is untested.
- **Training/test imbalance.** The data is split roughly 96/2.5/1, so the model was tuned and checked on very little held-out data.

## How to Use

Use the code from your Roboflow project's **Deploy** tab (API key required; keep it private). See the README for an example.

## Citation

```bibtex
@software{caguete2026sethsightvision2,
  author = {Caguete, Justine Clarke},
  title  = {SethSightVision2: ESP32-CAM Live Detection and ViT Fabric Classification},
  year   = {2026},
  url    = {https://github.com/negussmr/SethSightVision2}
}
```

Please also cite the Vision Transformer paper:

```bibtex
@inproceedings{dosovitskiy2021vit,
  title     = {An Image is Worth 16x16 Words: Transformers for Image Recognition at Scale},
  author    = {Dosovitskiy, Alexey and Beyer, Lucas and Kolesnikov, Alexander and Weissenborn, Dirk and Zhai, Xiaohua and Unterthiner, Thomas and Dehghani, Mostafa and Minderer, Matthias and Heigold, Georg and Gelly, Sylvain and Uszkoreit, Jakob and Houlsby, Neil},
  booktitle = {ICLR},
  year      = {2021}
}
```
