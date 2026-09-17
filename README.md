# ☀️ Solar AI — Smart Solar Plant Monitoring & AI Diagnostic Dashboard

An end-to-end 3-tier IoT telemetry, monitoring, and predictive maintenance platform for solar power systems. The platform ingests real-time solar panel metrics, stores historical bucketed data, predicts faults using Machine Learning, and provides natural-language troubleshooting through LLM-powered diagnostics.

---

## 🏗️ Architecture & Data Flow

```text
[ IoT Telemetry Simulator ]
          │
          ├── (Every 3s via Socket.IO) ────────► [ React Dashboard (UI) ]
          │                                              │
          └── (15-min Buckets) ──► [ MongoDB ]            ├── (LLM Diagnostics) ──► [ Groq API (Llama 3.1) ]
                                                         │
                                                         └── (Predictions) ──────► [ FastAPI ML Service ]

```

1. **Telemetry Simulation & Ingestion:** The Node.js backend (`liveDataSimulator.js`) generates real-time telemetry metrics (voltage, current, power, panel temperature, battery percentage, motor speed, and system error codes).
2. **Real-time Push & Storage:** Live readings are broadcasted to the frontend every 3 seconds via WebSockets (`Socket.IO`). Data points are aggregated and saved to MongoDB in 15-minute intervals.
3. **ML-Powered Analysis:** Telemetry data is sent to a Python FastAPI microservice for unsupervised anomaly detection and energy output forecasting.
4. **GenAI Diagnostics:** The backend leverages the Groq API running Llama 3.1 8B Instant to generate actionable diagnostic reports and maintenance recommendations based on active system alerts.

---

## ⚡ Key Features

* 📊 **Live Telemetry Stream:** Real-time updates for voltage, current, battery level, temperature, and operating status.
* 🤖 **GenAI Automated Diagnostics:** Natural language failure summaries and step-by-step resolution paths powered by LLMs.
* 🔍 **Anomaly Detection:** Unsupervised fault detection using Isolation Forest to identify equipment over-temperature, electrical short circuits, or voltage dropouts.
* 📈 **Power Yield Forecasting:** Time-series predictive models for estimating upcoming power production.
* 🎛️ **Multi-View UI:** Dedicated panels for Live Status, Historical Reports, Active Alerts, System Administration, and AI Intelligence.

---

## 🛠️ Tech Stack

| Layer | Technologies |
| --- | --- |
| **Frontend** | React 18, TypeScript, Vite, Tailwind CSS, Chart.js (`react-chartjs-2`), Socket.io-client |
| **Backend** | Node.js, Express 5, MongoDB (Mongoose), Socket.io, Axios, Groq SDK (`@google/generative-ai`) |
| **ML Microservice** | Python 3.x, FastAPI, Scikit-Learn, Pandas, NumPy, Joblib |

---

## 📂 Project Structure

```text
Solar-Power-main/
├── BACKEND/
│   ├── controllers/      # AI diagnostics (aiController.js) & core endpoints
│   ├── models/           # Mongoose database schemas (Readings.js)
│   ├── routes/           # Express API route declarations
│   ├── utils/            # liveDataSimulator.js (Telemetry generator)
│   ├── package.json
│   └── server.js         # Entry point (Express & Socket.IO server)
│
├── FRONTEND/
│   ├── src/
│   │   ├── components/   # UI Tabs (Admin, LiveStatus, Reports, Alerts, AIIntelligence)
│   │   ├── App.tsx       # Main dashboard layout and Socket listener
│   │   └── main.tsx
│   ├── package.json
│   └── vite.config.ts
│
└── ml-services/
    ├── models/           # Pre-trained ML artifacts (.pkl files)
    ├── main.py           # FastAPI service & inference endpoints
    ├── preprocessing.py  # Data cleaning and feature scaling routines
    └── requirements.txt  # Python dependencies

```

---

## 🤖 Machine Learning & AI Diagnostics

### 1. Anomaly Detection

Uses an **Isolation Forest** model (`sklearn.ensemble.IsolationForest`) evaluating `[voltage, current, power, temperature]`. It isolates spatial outliers to detect failure modes like zero-current voltage leaks or critical thermal overloads (>85°C).

### 2. Output Forecasting

Evaluates recent production trends using a regression pipeline to project expected power yield for upcoming time steps.

> **Note:** The FastAPI service includes built-in rule-based fallback logic (moving averages and threshold heuristics) to maintain service continuity if pre-trained `.pkl` artifacts are missing from the `ml-services/models/` directory.

### 3. LLM Diagnostic Layer

Communicates with `Groq` (Llama 3.1 8B Instant) via `aiController.js` to convert telemetry arrays and error codes into human-readable fault explanations and step-by-step troubleshooting actions.

---

## 🚀 Getting Started

### Prerequisites

* **Node.js** (v18+) & **npm**
* **Python** (v3.9+)
* **MongoDB** (Local instance or MongoDB Atlas URI)
* **Groq API Key**

---

### Installation & Run Steps

#### 1. Backend Setup

```bash
cd BACKEND
npm install

```

Create a `.env` file in the `BACKEND` directory:

```env
PORT=5000
MONGO_URI=mongodb://localhost:27017/solar_db
GROQ_API_KEY=your_groq_api_key_here

```

Start the backend server:

```bash
npm start

```

#### 2. ML Microservice Setup

```bash
cd ml-services
python -m venv venv
# On Windows: venv\Scripts\activate | On Mac/Linux: source venv/bin/activate
pip install -r requirements.txt

```

Start the FastAPI service:

```bash
uvicorn main:app --reload --port 8000

```

#### 3. Frontend Setup

```bash
cd FRONTEND
npm install
npm run dev

```

Open your browser and navigate to `http://localhost:5173` to view the dashboard.

---

## 📋 Status & Roadmap

* [x] Real-time IoT telemetry generation and Socket.IO integration
* [x] React dashboard with Chart.js visualization
* [x] LLM diagnostic integration via Groq SDK
* [x] FastAPI inference engine with rule-based fallback fallback
* [ ] Hardware connection for physical FTP data ingest (`.dat` telemetry parsing)
* [ ] Model training scripts pipeline export to auto-refresh `.pkl` artifacts
