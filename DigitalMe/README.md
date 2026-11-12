# Digital Twin — Smart Water Distribution

## Project Structure

```
digital_twin/
├── backend/
│   ├── app.py                            ← Flask API
│   ├── simulation.py                     ← Simulation engine
│   ├── test_ann_predictions_2500_rows.csv ← ANN model output
│   └── requirements.txt
└── frontend/
    └── index.html                        ← Dashboard UI
```

## Setup & Run

### 1. Install backend dependencies
```bash
cd backend
pip install -r requirements.txt
```

### 2. Start the Flask backend
```bash
cd backend
python app.py
```
You should see:
```
Starting Digital Twin API on http://localhost:5000
```

### 3. Open the frontend
Just open `frontend/index.html` in your browser.  
No build step needed — it's plain HTML.

---

## API Endpoints

### POST `/api/simulate`
Runs the simulation with given parameters.

**Request body:**
```json
{
  "scenario": "normal",
  "tank_initial": 50,
  "pump_input": 60,
  "leak_probability": 0.0,
  "tank_max": 100
}
```

**Scenarios:** `normal` | `high_demand` | `pump_failure` | `demand_spike`

**Response:**
```json
{
  "success": true,
  "tank_levels": [...],
  "pressures": [...],
  "demands": [...],
  "leak_losses": [...],
  "alerts": [{"step": 10, "message": "Low Tank Level", "type": "danger"}],
  "summary": {
    "avg_tank": 54.04,
    "min_tank": 10.96,
    "max_tank": 88.55,
    "avg_pressure": 21.62,
    "max_pressure": 35.42,
    "avg_demand": 60.95,
    "alert_count": 0,
    "total_steps": 2500
  }
}
```

### GET `/api/health`
Returns API status and number of demand points loaded.

---

## How It Works

1. Frontend sends user-selected parameters to the Flask API  
2. Flask loads `test_ann_predictions_2500_rows.csv` (ANN model output)  
3. `run_simulation()` from `simulation.py` runs the full 2500-step simulation  
4. Results are returned as JSON  
5. Frontend renders 4 live charts + stat cards + alerts
