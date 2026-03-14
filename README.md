<div align="center">

# 🧠 FlowDesk
### AI-Powered Smart Productivity Assistant

[![React](https://img.shields.io/badge/React-18-61DAFB?style=flat-square&logo=react)](https://reactjs.org/)
[![TensorFlow.js](https://img.shields.io/badge/TensorFlow.js-BlazeFace-FF6F00?style=flat-square&logo=tensorflow)](https://www.tensorflow.org/js)
[![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=flat-square&logo=python)](https://python.org/)
[![Flask](https://img.shields.io/badge/Flask-REST_API-000000?style=flat-square&logo=flask)](https://flask.palletsprojects.com/)
[![DistilBERT](https://img.shields.io/badge/DistilBERT-Fine--tuned-FFD21E?style=flat-square&logo=huggingface)](https://huggingface.co/)
[![Groq](https://img.shields.io/badge/Groq-LLaMA_3.3_70B-F55036?style=flat-square)](https://groq.com/)
[![Firebase](https://img.shields.io/badge/Firebase-Auth-FFCA28?style=flat-square&logo=firebase)](https://firebase.google.com/)
[![Chrome](https://img.shields.io/badge/Chrome-Extension_MV3-4285F4?style=flat-square&logo=googlechrome)](https://developer.chrome.com/)
[![License](https://img.shields.io/badge/License-MIT-green?style=flat-square)](LICENSE)
[![Cost](https://img.shields.io/badge/Total_Cost-$0.00-brightgreen?style=flat-square)]()

<br/>

> **FlowDesk** is a full-stack AI productivity system that monitors your cognitive presence using computer vision, classifies your browser activity using a self-trained transformer model, enforces focus through a Chrome extension, and interacts with you through a natural language voice assistant — all running locally at zero cost.

<br/>

![FlowDesk Dashboard](https://raw.githubusercontent.com/username/flowdesk/main/assets/dashboard-preview.png)

</div>

---

## 📌 Table of Contents

- [What Makes This Different](#-what-makes-this-different)
- [System Architecture](#-system-architecture)
- [Modules](#-modules)
- [Tech Stack](#-tech-stack)
- [AI Model](#-ai-model---distilbert-classifier)
- [Installation](#-installation)
- [Running the Project](#-running-the-project)
- [Project Structure](#-project-structure)
- [Results](#-results)
- [Cost Breakdown](#-cost-breakdown)

---

## ⚡ What Makes This Different

Most productivity tools use **keyword lists** to classify what you are doing:

```javascript
// What everyone else does
if (url.includes("youtube")) return "distracted"
if (url.includes("github"))  return "productive"
```

FlowDesk **trains its own transformer model** on a custom labelled dataset and uses it as the only classifier — with a live confidence score on every prediction.

```
URL + Page Title → Fine-tuned DistilBERT → "distracted" (96.2% confidence)
```

It also detects **whether you are physically at your desk** using a neural network running entirely in your browser, and speaks to you through an **LLM-powered voice assistant** that understands natural language commands.

---

## 🏗 System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      FLOWDESK DASHBOARD                      │
│                    React — localhost:3000                     │
│                                                               │
│  ┌─────────────┐  ┌──────────────┐  ┌────────────────────┐  │
│  │   Module 2  │  │   Module 3   │  │     Module 4       │  │
│  │  Presence   │  │  Tab Nudge   │  │  Voice Assistant   │  │
│  │  BlazeFace  │  │  AI Classify │  │  Groq LLaMA 3.3   │  │
│  └──────┬──────┘  └──────┬───────┘  └────────────────────┘  │
│         │                │                                    │
└─────────┼────────────────┼────────────────────────────────────┘
          │                │
          │         ┌──────▼──────────────────────┐
          │         │   Chrome Extension (MV3)     │
          │         │   background.js              │
          │         │   content.js                 │
          │         │   manifest.json              │
          │         └──────┬───────────────────────┘
          │                │
          │         ┌──────▼──────────────────────┐
          │         │   Flask AI Model Server      │
          │         │   localhost:5001             │
          │         │   DistilBERT Fine-tuned      │
          │         │   99.46% Accuracy            │
          │         └─────────────────────────────┘
          │
   ┌──────▼──────────────────┐
   │   Firebase Auth         │
   │   Google OAuth          │
   │   Email/Password        │
   └─────────────────────────┘
```

---

## 📦 Modules

### Module 1 — Authentication
Firebase Authentication with Google OAuth and email/password login. Protected routes, persistent sessions, user profile management. Zero backend infrastructure — entirely serverless.

### Module 2 — Presence Detection (Computer Vision)
Real-time face detection using **TensorFlow.js + BlazeFace** neural network running entirely in the browser. No data leaves the device.

| State | Description | Trigger |
|-------|-------------|---------|
| 🟢 Active | Face detected, user at desk | Face visible in webcam |
| 🟡 Away | Face not detected briefly | No face for 10 seconds |
| 🔴 Absent | User left the desk | No face for 30+ seconds |

- Live focus score calculation
- Session timeline with presence history
- Analytics on session end

### Module 3 — Tab Activity Monitor (Chrome Extension MV3)
A Chrome Extension that captures the active tab every **1 second** and sends it to FlowDesk for AI classification.

```
chrome.tabs API → background.js → chrome.storage → content.js → postMessage → React
```

Every tab visit is logged with domain, title, presence status at time of visit, time spent, and AI classification with confidence score.

### Module 4 — AI Voice Assistant
Always-listening hands-free voice assistant powered by **Groq LLaMA 3.3-70b-versatile** (free tier).

**Automatic Alerts (no command needed):**
- Distracted tab opens → speaks immediately + starts 10 second countdown
- User absent from desk → speaks + repeats every 30 seconds
- Session timer ends → speaks once

**Natural Language Commands (say anything):**
```
"open somewhere to practice DSA"     → opens leetcode.com
"take me to my email"                → opens mail.google.com
"I need to learn React hooks"        → opens react.dev
"search Python interview questions"  → Google search
"how is my session going"            → speaks your focus score
```

**Tab Auto-Close System:**
When a distracted tab is detected the assistant warns the user verbally, displays a 10 second countdown with progress bar, then closes the tab automatically via the Chrome extension if not dismissed.

### Module 5 — Self-Trained AI Classifier
The core intelligence of FlowDesk. A **DistilBERT transformer** fine-tuned on a custom dataset replacing all keyword rules entirely.

**Pipeline:**
```
Custom Dataset (2474 rows) → Fine-tune DistilBERT → Flask API → React
```

**What the model knows:**
- Trained on real browser activity data — URLs and page titles
- Understands semantic meaning not just keyword matching
- Generalises to websites it has never seen
- Returns confidence score on every prediction

---

## 🛠 Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Frontend | React 18 | Dashboard UI |
| Auth | Firebase Auth | User authentication |
| Computer Vision | TensorFlow.js + BlazeFace | Face detection in browser |
| Browser Extension | Chrome MV3 | Tab monitoring and control |
| NLP Model | DistilBERT (fine-tuned) | Tab productivity classification |
| Model Training | HuggingFace Transformers + PyTorch | Training pipeline |
| Training Compute | Google Colab T4 GPU | Free GPU training |
| Model Server | Flask + flask-cors | Local REST API |
| Voice Assistant | Groq API (LLaMA 3.3-70b) | Natural language understanding |
| Speech Input | Browser SpeechRecognition API | Always-listening microphone |
| Speech Output | Browser SpeechSynthesis API | Text to speech |
| Styling | Custom CSS (DM Sans + Syne) | Dashboard design |

---

## 🧠 AI Model — DistilBERT Classifier

### Dataset
- **2474 labelled rows** built manually
- Features: `url + page_title` combined as text input
- Labels: `productive` / `distracted`
- Split: 70% train / 15% validation / 15% test

### Training
```python
Model     : distilbert-base-uncased
Epochs    : 4
Batch size: 32
LR        : 2e-5
GPU       : Google Colab T4 (free)
Framework : HuggingFace Transformers + PyTorch
```

### Results

| Metric | Score |
|--------|-------|
| **Accuracy** | **99.46%** |
| **F1 Score** | **99.46%** |
| Precision (productive) | 99.5% |
| Recall (productive) | 99.4% |
| Precision (distracted) | 99.4% |
| Recall (distracted) | 99.5% |

### Training Curves
![Training Curves](assets/training_curves.png)

### Confusion Matrix
![Confusion Matrix](assets/confusion_matrix.png)

### Live Inference
```bash
curl -X POST http://localhost:5001/classify \
     -H "Content-Type: application/json" \
     -d '{"text": "youtube.com funny cat videos compilation"}'

# Response:
{
  "label": "distracted",
  "confidence": 0.9834,
  "model": "FlowDesk-DistilBERT-Classifier",
  "time_ms": 42.1
}
```

---

## 📁 Project Structure

```
flowdesk/
├── src/
│   ├── App.jsx                  # Root component, routing
│   ├── firebase.js              # Firebase config
│   ├── Auth.jsx                 # Module 1 — Authentication
│   ├── PresenceModule.jsx       # Module 2 — Face detection + dashboard
│   ├── TabNudge.jsx             # Module 3 — Tab monitor + AI classify
│   └── VoiceAssistant.jsx       # Module 4 — LLM voice assistant
│
├── flowdesk-extension/
│   ├── manifest.json            # MV3 extension config
│   ├── background.js            # Service worker — tab capture, open/close
│   └── content.js               # Injected script — bridge to React
│
└── flowdesk-ml/
    ├── generate_dataset.py      # Dataset generation script
    ├── train_model.py           # Full training pipeline
    ├── serve.py                 # Flask API server
    └── flowdesk_model/          # Trained model files (after training)
        ├── config.json
        ├── model.safetensors
        ├── tokenizer.json
        └── model_info.json
```

---

## 🚀 Installation

### Prerequisites
- Node.js 18+
- Python 3.10+
- Google Chrome browser
- Groq API key (free at [console.groq.com](https://console.groq.com))
- Firebase project (free at [console.firebase.google.com](https://console.firebase.google.com))

### 1. Clone the repository
```bash
git clone https://github.com/username/flowdesk.git
cd flowdesk
```

### 2. Install React dependencies
```bash
npm install
```

### 3. Configure Firebase
Create `src/firebase.js` and add your Firebase config:
```javascript
import { initializeApp } from "firebase/app";
import { getAuth } from "firebase/auth";

const firebaseConfig = {
  apiKey:            "your-api-key",
  authDomain:        "your-project.firebaseapp.com",
  projectId:         "your-project-id",
  storageBucket:     "your-project.appspot.com",
  messagingSenderId: "your-sender-id",
  appId:             "your-app-id",
};

const app  = initializeApp(firebaseConfig);
export const auth = getAuth(app);
```

### 4. Configure Groq API Key
In `src/VoiceAssistant.jsx` line 26:
```javascript
const GROQ_API_KEY = "your-groq-api-key-here";
```

### 5. Install Python dependencies
```bash
cd flowdesk-ml
pip install flask flask-cors transformers torch
```

### 6. Train or download the model
**Option A — Train yourself (recommended for FYP):**
```bash
python generate_dataset.py        # generates dataset.csv
# Upload dataset.csv to Google Colab
# Run train_model.py on Colab with T4 GPU
# Download flowdesk_model.zip
unzip flowdesk_model.zip -d flowdesk_model
```

**Option B — Use pre-trained model:**
Download from [Releases](https://github.com/username/flowdesk/releases) and extract to `flowdesk-ml/flowdesk_model/`

### 7. Load Chrome Extension
```
1. Open Chrome → chrome://extensions
2. Enable Developer mode (top right)
3. Click "Load unpacked"
4. Select the flowdesk-extension/ folder
```

---

## ▶ Running the Project

Every time you work on FlowDesk open **three terminals**:

**Terminal 1 — AI Model Server:**
```bash
cd flowdesk-ml
python serve.py
# ✅ FlowDesk AI Model Server running on http://localhost:5001
```

**Terminal 2 — React Dashboard:**
```bash
cd flowdesk
npm start
# ✅ Compiled successfully — http://localhost:3000
```

**Chrome — Extension enabled:**
```
chrome://extensions → FlowDesk Tab Monitor → toggle ON
```

Open `http://localhost:3000`, sign in, and start a session.

---

## 📊 Results

### Model Performance
| Dataset Size | Accuracy | F1 Score | Training Time |
|-------------|---------|---------|--------------|
| 2474 samples | 99.46% | 99.46% | ~35 min (T4 GPU) |

### System Performance
| Feature | Latency |
|---------|---------|
| Face detection | Real-time (30fps) |
| Tab classification | ~42ms (local model) |
| Voice command response | ~800ms (Groq API) |
| Tab close execution | <100ms (extension) |

---

## 💰 Cost Breakdown

| Component | Technology | Cost |
|-----------|-----------|------|
| Authentication | Firebase Spark Plan | Free |
| Face Detection | TensorFlow.js (browser) | Free |
| Tab Monitoring | Chrome Extension APIs | Free |
| Model Training | Google Colab T4 GPU | Free |
| Model Serving | Local Flask server | Free |
| Voice Intelligence | Groq Free Tier (14,400 req/day) | Free |
| Speech APIs | Browser built-in | Free |
| **Total** | | **$0.00** |

---

## 🎓 FYP Context

This project was built as a Final Year Project for a Computer Science degree. It demonstrates:

- **Full ML Pipeline** — data collection, labelling, model training, evaluation, and deployment
- **Computer Vision** — real-time neural network inference in the browser
- **NLP and Transformers** — fine-tuning a 66M parameter transformer model
- **Browser Extension Development** — Chrome MV3 with service workers
- **LLM Integration** — prompt engineering, structured JSON output, natural language understanding
- **Full Stack Development** — React frontend, Flask backend, Firebase auth, Chrome extension

> *"I collected and labelled a custom dataset of 2474 browser activity samples, fine-tuned a DistilBERT transformer model achieving 99.46% accuracy, deployed it as a local REST API, and integrated it as the sole classifier in a real-time productivity system — replacing all rule-based classification entirely."*

---

## 📄 License

MIT License — see [LICENSE](LICENSE) for details.

---

<div align="center">

Built with ❤️ as a Computer Science Final Year Project

**[Live Demo](#) · [Report Bug](issues) · [Request Feature](issues)**

</div>
