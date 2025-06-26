# MediScan

MediScan is an AI-powered solution designed to identify damaged, blurry, or torn medicine packet names using **Computer Vision**, **OCR**, **NER**, and **Masked Language Modeling**. It is intended for real-time use with cameras on devices like Raspberry Pi or mobile phones.

---

## 🚀 Pipeline Overview

MediScan includes a modular pipeline where each component contributes to accurate detection:

### 1️⃣ **OpenCV Preprocessing**
- Applies denoising, CLAHE (contrast enhancement), thresholding, and morphology to improve image quality.
- Detects contours or colored regions (e.g. red or green text boxes on packets) to isolate text areas.

### 2️⃣ **OCR Extraction**
- Uses **Tesseract OCR** and **EasyOCR** to read text from both the entire image and specific detected regions.
- Supports multiple OCR strategies to handle blurry, occluded, or noisy text.

### 3️⃣ **NER + Custom Rules**
- Cleans the OCR text (removes unwanted characters, standardizes spacing).
- Uses **spaCy NER** to identify standard entities (like quantities, names).
- Adds custom regex rules to extract drug names (e.g. patterns like `Paracetamol 500`, `Dolo 650`).

### 4️⃣ **DistilBERT Masked Language Model**
- Handles partial or noisy drug names.
- **DistilBERT is fine-tuned** on a dataset of real medicine names where:
  - Input: Partial/blurred names (e.g. `Dolo 6`)
  - Target: Full names (e.g. `Dolo 650`)
- In the pipeline:
  - The cleaned OCR + NER output is fed into the DistilBERT masked LM.
  - It predicts missing or corrupted parts using `[MASK]` tokens.

## Project Structure
 ```
MediScan/
├── app/
│   ├── __init__.py
│   └── pipeline.py               # The main pipeline code: OpenCV + OCR + NER + DistilBERT
├── train/
│   ├── __init__.py
│   └── distilbert_train.py       # DistilBERT fine-tuning code
├── requirements.txt              # Dependencies
└── README.md                     # This file

 ```

## IoT & Mobile Camera Integration
Raspberry Pi / USB cam:
- Use cv2.VideoCapture(0) to capture frame → save image → feed to pipeline.
Example:
    import cv2
    from app.pipeline import run_mediscan

    cam = cv2.VideoCapture(0)
    ret, frame = cam.read()
    if ret:
        cv2.imwrite("capture.jpg", frame)
        run_mediscan("capture.jpg")
    cam.release()

Mobile App:
- Build a frontend (e.g. Flutter / React Native).
- Send image to backend (Flask/FastAPI) where pipeline runs.

IoT:
- Use MQTT, WebSockets, or HTTP to send captured frames to a server running MediScan.

## Planned Enhancements
- Add CNN or YOLO-based detection for better salt-name and block extraction  
- Convert to TensorRT/ONNX for faster inference on edge devices  
- Dashboard for visualizing predictions  

## Credits
Developed as part of the MediScan project — AI + IoT for medicine packet safety.


# AIpipelineforMedicinenamerecogn
