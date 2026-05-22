# Fuzzy Logic Project

This repository contains exploratory fuzzy-logic work organized by contributor folders.

## Repository Structure

- `ashani/` — workspace folder (currently minimal scaffold)
- `lahiru/` — workspace folder (currently minimal scaffold)
- `ravindu/` — active implementation area
  - `football-analysis/` — football object detection experiments using YOLO + fuzzy logic

## Getting Started

The most complete project currently lives in:

`/ravindu/football-analysis`

From the repository root:

```bash
cd ravindu/football-analysis
```

Then follow its local setup and usage guide in:

`ravindu/football-analysis/README.md`

# Football Analysis with YOLO + Fuzzy Logic

This project explores football (soccer) object detection by combining:

- **YOLOv8** for baseline object detection (`person`, `sports ball`)
- **Fuzzy logic rules** to refine confidence for ball detections
- **OpenCV visualizations** to compare baseline vs adjusted confidences

---

## Table of Contents

- [Project Purpose](#project-purpose)
- [Repository Context](#repository-context)
- [Current Capabilities](#current-capabilities)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Usage](#usage)
- [Inputs and Outputs](#inputs-and-outputs)
- [Fuzzy Rule Design Notes](#fuzzy-rule-design-notes)
- [Testing](#testing)
- [Troubleshooting](#troubleshooting)
- [Limitations and Known Gaps](#limitations-and-known-gaps)
- [Future Improvements](#future-improvements)

---

## Project Purpose

Raw detector confidence is often noisy for football images. This project adds domain-inspired fuzzy reasoning (position, aspect ratio, person-ball relationship, etc.) to produce an adjusted confidence score intended to better reflect likely true-ball detections.

---

## Repository Context

This README documents:

`/ravindu/football-analysis`

in the broader `fuzzy-logic-project` repository.

---

## Current Capabilities

- Detects persons and sports balls using **Ultralytics YOLOv8**.
- Applies fuzzy inference to ball detections.
- Produces a side-by-side visualization:
  - left: original detections
  - right: fuzzy-adjusted ball confidences
- Supports image and video experimentation scripts.

---

## Project Structure

```text
football-analysis/
├── football_analysis/
│   ├── __init__.py
│   ├── fuzzy.py                  # early fuzzy experiment
│   ├── fuzzy_image_driver.py     # main comparison visualization for images
│   ├── fuzzy_rules.py            # fuzzy system and confidence adjustment logic
│   ├── image_analysis.py         # video detection/tracking script
│   └── main.py                   # simple YOLO image detection viewer
├── docs/
│   └── fuzzy-rule-ideas.md       # notes and rule brainstorming
├── media/                        # sample media inputs
├── tests/
│   └── __init__.py
├── pyproject.toml
└── README.md
```

---

## How It Works

1. YOLO predicts bounding boxes and confidence.
2. Ball detections are extracted (`class_id == 32`).
3. Additional attributes are derived, including:
   - normalized vertical/horizontal position
   - aspect ratio
   - person-to-ball size ratio proxy
4. Fuzzy rules generate an intermediate confidence.
5. A composite formula blends initial and fuzzy confidence.

Core logic lives in:

- `football_analysis/fuzzy_rules.py`
- `football_analysis/fuzzy_image_driver.py`

---

## Prerequisites

- **Python 3.12+**
- **Poetry**
- Internet access on first model run (to fetch `yolov8n.pt`) unless already cached

Main dependencies include:

- `ultralytics`
- `opencv-python`
- `scikit-fuzzy`
- `numpy`
- `matplotlib`
- `tqdm`
- `screeninfo`
- `torch`
- `torchvision`

---

## Installation

From the repository root:

```bash
cd ravindu/football-analysis
```

Install dependencies:

```bash
poetry install
```

Activate shell (optional):

```bash
poetry shell
```

Exit shell:

```bash
exit
```

---

## Usage

### 1) Main fuzzy comparison on image

```bash
poetry run python -m football_analysis.fuzzy_image_driver
```

What this does:

- reads `media/football-girls.jpg`
- runs YOLO detections
- computes adjusted ball confidence via fuzzy rules
- opens a comparison window
- writes `detection_comparison.jpg`

---

### 2) Basic YOLO image viewer

```bash
poetry run python -m football_analysis.main
```

This runs plain YOLO detection and displays annotations.

---

### 3) Video processing experiment

```bash
poetry run python -m football_analysis.image_analysis
```

This script processes a configured input video and writes an annotated output video.

> Note: The current default video filename in `image_analysis.py` is not present in `media/` by default. Update the filename/path before running.

---

## Inputs and Outputs

### Inputs

- Images/videos in `media/`
- YOLO model weights (`yolov8n.pt`, auto-loaded by Ultralytics)

### Outputs

- Visualization window(s)
- `detection_comparison.jpg` (for fuzzy image driver)
- Optional processed video output (from `image_analysis.py`)
- Optional logs from video analysis script

---

## Fuzzy Rule Design Notes

Rule ideation is documented in:

- `docs/fuzzy-rule-ideas.md`

Implemented fuzzy features currently include:

- vertical position weighting
- horizontal edge/middle weighting
- person-ball ratio influence
- aspect ratio circularity influence
- initial YOLO confidence as an additional fuzzy signal

---

## Testing

Run tests from this directory:

```bash
poetry run pytest
```

Coverage command (if tests are added/expanded):

```bash
poetry run pytest --cov=football_analysis
```

---

## Troubleshooting

- **`poetry: command not found`**
  - Install Poetry and ensure it is in your shell `PATH`.

- **Model download or load issues**
  - Ensure internet access for first run, or manually provide local weights.

- **No image/video window appears**
  - Confirm GUI support for OpenCV in your environment.

- **`Error: Cannot open image/video`**
  - Verify file path and filename in the script configuration.

- **Windows-style paths on Linux/macOS**
  - Some scripts use `\\` in strings; prefer forward slashes when editing paths.

---

## Limitations and Known Gaps

- Test suite is currently minimal.
- Some fuzzy-rule experimentation code exists alongside newer logic.
- Video script defaults may reference non-existent local files.
- Person-to-ball ratio uses a simplified closest-person approximation.
- A few TODO comments indicate rule tuning is still in progress.

---

## Future Improvements

- Add unit tests for fuzzy rule behavior and edge cases.
- Add configurable CLI arguments (input path, model path, thresholds, output path).
- Add calibration/benchmark scripts for confidence improvement quality.
- Improve person-ball association logic (distance-based nearest player).
- Standardize path handling and logging.

---

If you are extending this project, start with:

- `football_analysis/fuzzy_rules.py` for inference logic
- `football_analysis/fuzzy_image_driver.py` for visualization and integration

