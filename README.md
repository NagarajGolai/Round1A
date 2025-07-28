# PDF Outline Extractor using Heuristic Approach
---

## 📚 Table of Contents

- [🧠 Overview](#-overview)
- [⚙️ Approach](#️-approach)
- [🗂 Directory Structure](#-directory-structure)
- [📦 Dependencies](#-dependencies)
- [🐳 Docker Setup](#-docker-setup)
  - [Build Docker Image](#️-build-docker-image)
  - [Run Docker Container](#-run-docker-container)
- [📌 Assumptions and Notes](#-assumptions-and-notes)
- [🛠️ Noteworthy Technical Highlights](#️-noteworthy-technical-highlights)
- [📚 How to Train Thresholds](#-how-to-train-thresholds)
- [🔄 How to Extend](#-how-to-extend)

---

## 🧠 Overview

This project provides an automated PDF outline extractor using a **heuristic approach**. It analyzes font size and position data from PDFs to infer heading structure and builds structured JSON outlines — no AI/ML required.

🧪 Built during a Hackathon, it is:
- ✅ **Accurate** (tunable via training data)
- 📦 **Portable** (Docker-ready)
- 🔌 **Offline-compatible**
- 💻 **Cross-platform** (macOS, Linux, Windows via WSL2)

---

## ⚙️ Approach

1. **Text Extraction**  
   Uses `PyMuPDF (fitz)` to extract text blocks with font size and coordinates.

2. **Heuristic Training**  
   `heuristic_train.py` computes font thresholds using labeled data (`training_data.csv`).

3. **Outline Inference**  
   `extract_outline.py` tags headings based on thresholds.

4. **Structured Output**  
   Output is clean JSON like:

   ```json
   {
     "title": "Document Title",
     "outline": [
       { "level": "H1", "text": "Introduction", "page": 1 },
       { "level": "H2", "text": "Background", "page": 2 },
       { "level": "H3", "text": "Details", "page": 3 }
     ]
   }
   ```

---

## 🗂 Directory Structure

```
Round1A/
├── Dockerfile
├── heuristic_train.py
├── extract_outline.py
├── prepare_training_data.py
├── training_data.csv
├── heading_thresholds.json
├── /input          # Place input PDFs here
├── /output         # JSON outputs will be written here
└── requirements.txt
```

---

## 📦 Dependencies

- Python 3.10
- PyMuPDF
- numpy

---

## 🐳 Docker Setup

### 🛠️ Build Docker Image

```bash
docker build --platform linux/amd64 -t pdf-outline-extractor:latest .
```
> ℹ️ **Notes:**
> - This system runs **entirely offline** after the Docker image is built.
> - An **internet connection is required only during the image build process** to download required packages and dependencies.
> - The **`ideal_output/` folder (training data)** is **only needed while building** the Docker image. After building, you can **safely delete the training files**, and the pipeline will still work correctly during execution.

### 🚀 Run Docker Container

Mac/Linux:
```
docker run --rm -v "$(pwd)/input:/app/input" -v "$(pwd)/output:/app/output" round1b-pipeline
```
Windows PowerShell:
```
docker run --rm -v "${PWD}\input:/app/input" -v "${PWD}\output:/app/output" round1b-pipeline
```
Windows CMD:
```
docker run --rm -v "%cd%\input:/app/input" -v "%cd%\output:/app/output" round1b-pipeline
```

> 💡 This works on **macOS**, **Linux**, and **Windows via WSL or Git Bash**.  
> 🪟 For **Windows CMD/PowerShell**, replace `$(pwd)` with the full path (e.g., `C:/Users/You/Project/input`).

📤 This command processes all PDFs in the `/input` folder and writes JSON files to `/output`.

---

## 📌 Assumptions and Notes

- 🧠 Heuristic logic only — no ML models used.
- ⚙️ CPU-based, no GPU required.
- 🚫 No internet dependency during execution.
- 🚀 Processes 50-page PDFs in under 10 seconds (on 8-core, 16 GB RAM).
- 🐋 Docker image is based on `python:3.10-slim` with `linux/amd64` compatibility.

---

## 🛠️ Noteworthy Technical Highlights

- ✅ Fully offline — ideal for sensitive/secure PDFs  
- 🔧 Customizable thresholds via retraining script  
- 🧘 Model-free and GPU-free: simple deployment  
- 🖥️ Runs on macOS (Intel + M1/M2), Linux, Windows (via WSL2)  
- 🔒 Isolated Docker execution with `--network none`  
- ⏱️ Optimized for <10s processing for standard docs

---

## 📚 How to Train Thresholds

```bash
python heuristic_train.py --input training_data.csv --output heading_thresholds.json
```

---
## Note :
This system runs completely offline once built. However, an internet connection is required only while building the Docker image (to download dependencies). After that, it can be run without internet access.
