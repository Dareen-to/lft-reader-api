# PyTorch checkpoints

**You do not need anything in this folder to run the API.** The service loads
the ONNX graphs in [`../models/`](../models), which are tracked in this repo.
This folder matters only if you want to re-run
[`../export_onnx.py`](../export_onnx.py) and regenerate them yourself.

| File | Tracked here? | Where it is |
|---|---|---|
| `best.pt` (YOLOv8s-OBB detector, 23 MB) | No | [`best.pt`](https://github.com/Jawlan428/Deep-Learning/blob/main/best.pt) in the original course repo |
| `classifier_mnv3.pt` (MobileNetV3-Small, ~6 MB) | No | Not committed anywhere — see below |

## About the classifier checkpoint

`classifier_mnv3.pt` was never committed to the original repo; it is a training
output, and only the exported `classifier.onnx` survives. So the classifier half
of `export_onnx.py` cannot be reproduced from a clean checkout without
retraining via `train_classifier_mobilenet.py` in the original repo.

This is stated rather than papered over. It does not affect the service — the
ONNX graph is the artifact the container loads, and its fidelity to the original
checkpoint is verified and recorded in
[`../models/export_report.json`](../models/export_report.json): max logit
difference 3.4e-05, zero label disagreements across the 81-image held-out set.

## Regenerating the detector ONNX

```bash
# fetch the checkpoint from the original repo into this folder
curl -L -o best.pt \
  https://github.com/Jawlan428/Deep-Learning/raw/main/best.pt

cd ..
pip install torch torchvision ultralytics   # ~2 GB, which is the whole reason
python export_onnx.py                       # the deployed image avoids them
```

Override the lookup paths with the `DETECTOR_PT` and `CLASSIFIER_PT`
environment variables if your checkpoints live elsewhere.
