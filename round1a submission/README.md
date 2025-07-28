# Adobe India Hackathon - Round 1A: Document Outline Extractor

## 📄 Project Overview

This project is a submission for **Round 1A** of the **Adobe India Hackathon**, themed **"Connecting the Dots: Understand Your Document"**.

The core objective is to build a system that intelligently extracts structured outlines from PDF documents — including the **main title** and hierarchical headings (**H1**, **H2**, **H3**) — and outputs this data in a clean, standardized **JSON format**. This outline provides the foundation for enhanced document experiences like semantic search, recommendation systems, and insight generation.

---

## ✨ Features

- **PDF Parsing:** Efficient extraction of structured text content using **PyMuPDF**.
- **Title Extraction:** Dynamically identifies the document title based on font size and position.
- **Hierarchical Heading Detection:** Detects and categorizes headings as **H1**, **H2**, or **H3**.
- **Robust Logic with Heuristics:**
  - **Font Size Analysis:** General approach based on detecting visually prominent text.
  - **Heuristic Pattern Matching:** For PDFs with inconsistent heading styles.
  - **Heuristic-based Pattern Matching:** Specialized logic for documents where font size differences for headings are subtle or inconsistent, relying on textual patterns and structural cues.
- **Heading Validation:** Filters out irrelevant or misleading text like dates, URLs, or short lines.
- **JSON Output:** Neatly structured JSON containing title, headings, their levels, and page numbers.
- **Dockerized:** Containerized for reliable, reproducible, and offline execution.

---

## 🧠 Technical Approach

### 1. **PDF Parsing**
- Utilizes `fitz` (PyMuPDF) to process PDFs page-by-page.
- Extracts spans from blocks, collecting `text`, `font_size`, `page`, and vertical position (`y0`).

### 2. **Body Font Size Detection**
- Median font size is computed across all spans to establish the document's body text size.

### 3. **Heading Detection Logic**
- **Standard PDFs:**
  - Font sizes greater than body font size + threshold are classified as headings.
  - Unique font sizes are mapped to heading levels (H1 → largest, H2 → second largest, etc.).
- **Pattern-Based PDFs:**
  - For specialized types (like numbered outlines), the script detects structured patterns (e.g., 1., 1.1., 1.1.1).

### 4. **Special Handling for Edge Cases**
- Detects file types like:
  - Adobe Hackathon prompt (file04)
  - LTC Form, Posters, Proposals
- Applies custom title and heading logic accordingly.

### 5. **Title Extraction**
- Flexible strategy:
  - Largest valid heading on page 0
  - Concatenation of top lines (if needed)

### 6. **Refinement & Deduplication**
- All headings undergo final validation.
- Sorted and deduplicated using `(text.lower(), page, y0)` as identifier.

---

## 📦 Libraries and Dependencies

- [`fitz` (PyMuPDF)] — PDF parsing and layout analysis
- `json` — Output formatting
- `os` — File system interaction
- `re` — Regular expressions for filtering and pattern detection
- `statistics` — For computing median font size
- `collections.Counter` — Used in title frequency heuristics

All dependencies are specified in `requirements.txt`.

---

## 🚀 Getting Started

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- Compatible with **linux/amd64** architecture

### 1. Build the Docker Image

```bash
cd /path/to/your/project

docker build --platform linux/amd64 -t round1a-outline-extractor .
```

### 2. Prepare Input Files

Place your PDF file (`file04.pdf`) inside an `input/` directory:

```
project_root/
├── Dockerfile
├── extractor.py
├── requirements.txt
├── input/
│   └── file04.pdf
```

### 3. Run the Solution

```bash
docker run --rm -v "${PWD}/input:/app/input" -v "${PWD}/output:/app/output" round1a-outline-extractor
```

The output will be saved inside the `output/` folder.

---

## 📁 Input and Output Format

### Input

PDF documents placed in `/app/input` (mapped from your local `input/` folder).

### Output

For each input file like `file04.pdf`, a `file04.json` will be generated with structure:

```json
{
  "title": "Parsippany -Troy Hills STEM Pathways",
  "outline": [
    { "level": "H1", "text": "PATHWAY OPTIONS", "page": 0 }
  ]
}
```

---

## ✅ Hackathon Compliance

| Constraint           | Status       |
| -------------------- | ------------ |
| CPU-only             | ✅ Supported  |
| Architecture (amd64) | ✅ Compatible |
| Offline Execution    | ✅ No APIs    |
| Model Size           | ✅ No model   |
| ≤ 10s for 50 pages   | ✅ Optimized  |

---