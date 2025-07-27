# PDF Outline Extractor using Heuristic Approach

## Overview

This project provides an automated PDF outline extractor using a heuristic approach. It leverages font size and positioning data from text blocks (via PyMuPDF) to identify hierarchical headings (like Title, H1, H2, H3) and generates structured JSON outlines from raw PDF files. 

It was built as part of a Hackathon challenge and designed to be:
- **Accurate** (with tunable heuristics based on training data),
- **Portable** (via Docker),
- **Offline-compatible** (no internet required at runtime),
- **Platform-independent** (tested on macOS and Linux).

## Approach

1. **Text Extraction**:  
   Utilizes `PyMuPDF (fitz)` to extract text blocks along with their font sizes and coordinates.

2. **Heuristic Training**:  
   A script (`heuristic_train.py`) processes labeled data (from `training_data.csv`) to compute dynamic font size thresholds for heading levels (Title, H1, H2, H3).

3. **Outline Inference**:  
   The main script (`extract_outline.py`) uses these thresholds to infer and tag headings per page.

4. **Structured Output**:  
   Produces a clean `JSON` structure with the outline in:
   ```json
   {
     "title": "Document Title",
     "outline": [
       { "level": "H1", "text": "Introduction", "page": 1 },
       { "level": "H2", "text": "Background", "page": 2 },
       { "level": "H3", "text": "Details", "page": 3 }
     ]
   }

## 🗂 Directory Structure

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

## Dependencies

- Python 3.10
- PyMuPDF
- numpy

## 🐳 Docker (Cross-platform: macOS, Linux, Windows with WSL2)

The project includes a Dockerfile to build a containerized environment for easy deployment.
To build the Docker image (compatible with most systems):

```bash
docker build --platform linux/amd64 -t pdf-outline-extractor:latest .

## 🐳 Run Docker Container

```bash
docker run --rm \
  -v $(pwd)/input:/app/input \
  -v $(pwd)/output:/app/output \
  --network none \
  pdf-outline-extractor:latest

This command works on macOS, Linux, and Windows (via WSL or Git Bash).
For Windows CMD or PowerShell, replace $(pwd) with the full path (e.g., C:/Users/You/Project/input).
This command processes all PDFs in the `input` folder and writes JSON outputs to the `output` folder.

## Assumptions and Notes

- The solution uses a heuristic approach without deep learning models.
- The solution runs on CPU only.
- The solution does not make any network calls and runs offline.
- The processing time for a 50-page PDF should be under 10 seconds on a system with 8 CPUs and 16 GB RAM.
- The Docker image is based on `python:3.10-slim` with AMD64 platform compatibility.

## Noteworthy Technical Notes

✅ Works fully offline after build — great for private document processing.
✅ Automatically adapts to new document styles via the retraining script.
🚫 Does not require internet for PDF processing (inference).
✅ Model-free: avoids complex deployment or GPU dependencies.
🖥️ Compatible with both macOS (M1/M2) and Intel-based systems via Docker’s platform specification.
🛠️ Custom threshold tuning offers flexibility per document type or style.
🔒 Docker --network none ensures safe, isolated container runs.
⏱️ Optimized: <10s processing time for 50-page PDF on 8-core CPU.

## How to Train Thresholds

- Use the `heuristic_train.py` script to compute font size thresholds from the training data CSV (`training_data.csv`).
- The script outputs `heading_thresholds.json` which is used by the extractor to assign heading levels.

## How to Extend

- To prepare training data, use `prepare_training_data.py`.
docker run --rm -v $(pwd)/input:/app/input -v $(pwd)/output:/app/output --network none pdf-outline-extractor:latest
