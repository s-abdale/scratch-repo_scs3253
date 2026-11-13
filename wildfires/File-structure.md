# Folder structure and pseudocode blueprint for the wildfire project


### Suggested Project Folder Structure

```text
wildfire-prediction-project/
│
├── data/
│   ├── raw/                 # Original CSVs from NASA FIRMS & POWER
│   └── processed/           # Cleaned and merged master datasets
│
├── python-ml/
│   ├── data_pipeline.py     # Member 1: Ingestion & merging
│   ├── feature_eng.py       # Member 2: Lags & transformations
│   ├── models.py            # Member 3 & 4: Classification, Regression, Clustering
│   └── export_results.py    # Outputs final predictions to JSON for the frontend
│
└── visualizer/              # Member 4 (You!): Frontend App
    ├── index.html           # Main dashboard layout
    ├── styles.css           # Styling (Tailwind or custom CSS)
    ├── app.js               # JavaScript logic for interactive UI & charts
    └── data.json            # Model outputs consumed by JavaScript

```

---

### Pseudocode Code Skeletons

#### 1. `python-ml/data_pipeline.py` (Member 1)

```python
# FILE TYPE: Python Script (.py)
# WHAT IT DOES: Filters global NASA FIRMS/POWER data for Canada, handles missing values, and merges them on date & region coordinates.
# INPUTS: Raw CSV files from NASA FIRMS (fires) and NASA POWER (weather).
# OUTPUTS: A cleaned merged CSV file saved to `data/processed/merged_canada_data.csv`.

def load_and_filter_firms():
    # Load raw FIRMS global data
    # Filter rows where latitude/longitude fall within Canada's bounding box
    # Return filtered fire dataframe
    pass

def load_and_merge_weather(fire_df):
    # Load NASA POWER weather data (temperature, wind speed, humidity)
    # Merge weather dataframe with fire dataframe based on matching Date and Region ID
    # Handle missing values using imputation (Chapter 2 / 4 techniques)
    pass

if __name__ == "__main__":
    # Run pipeline steps and save output to data/processed/merged_canada_data.csv
    pass

```

#### 2. `python-ml/feature_eng.py` (Member 2)

```python
# FILE TYPE: Python Script (.py)
# WHAT IT DOES: Creates lag features (e.g., past 7 days weather) and formats target variables for ML.
# INPUTS: `data/processed/merged_canada_data.csv`
# OUTPUTS: Feature-engineered dataset saved to `data/processed/features.csv`.

def create_lag_features(df):
    # Create rolling averages for temperature and precipitation over the last 7 and 14 days
    # This helps models see dry spells leading up to a fire
    pass

def define_targets(df):
    # Regression target: Max daily Fire Radiative Power (FRP)
    # Classification target: Binary high-intensity flag (1 if FRP > threshold, else 0)
    pass

if __name__ == "__main__":
    # Load merged data, apply transformations, output features.csv
    pass

```

#### 3. `python-ml/models.py` (Member 3 & 4)

```python
# FILE TYPE: Python Script (.py)
# WHAT IT DOES: Trains K-Means clustering (Ch 9), Logistic Regression (Ch 3), Random Forest, and Gradient Boosting (Ch 6 & 7). Evaluates them using Precision, Recall, and F1-Score.
# INPUTS: `data/processed/features.csv`
# OUTPUTS: Trained model objects and evaluation metric reports printed to console.

from sklearn.cluster import KMeans
from sklearn.ensemble import RandomForestClassifier, GradientBoostingClassifier
from sklearn.metrics import classification_report, precision_recall_fscore_support

def run_clustering(df):
    # Apply K-Means clustering (Ch 9) to group regions by climate profile
    # Append cluster ID as a new feature column
    pass

def train_supervised_models(X_train, y_train, X_test, y_test):
    # Train Baseline: Logistic Regression
    # Train Ensembles: Random Forest and Gradient Boosting (Ch 6 & 7)
    # Evaluate using Precision, Recall, and F1-Score (to handle class imbalance)
    pass

```

#### 4. `visualizer/app.js` (Member 4 - Your JavaScript Frontend!)

```javascript
// FILE TYPE: JavaScript File (.js)
// WHAT IT DOES: Loads the model predictions / summary data and renders interactive charts and risk filters in the browser.
// INPUTS: `data.json` (exported from Python model results)
// OUTPUTS: Dynamic HTML elements, charts, and maps rendered inside `index.html`.

// Load model output data
async function loadDashboardData() {
    // const response = await fetch('data.json');
    // const data = await response.json();
    // renderCharts(data);
}

// Handle user interactions (e.g., sliding weather variables or selecting a Canadian region)
function setupEventListeners() {
    // Listen for slider changes (e.g., temperature or wind speed adjustments)
    // Recalculate or display mock prediction outcome on the UI
}

document.addEventListener('DOMContentLoaded', () => {
    loadDashboardData();
    setupEventListeners();
});

```

---

## Exporting model results from Python to JSON for the visualizer

Will connect the machine learning backend and JavaScript frontend visualizer with Python's built-in `json` module.


### 1. Python Script to Export (`python-ml/export_results.py`)

Here is how you package up your model's performance metrics, feature importances, or region-by-region predictions into a JSON file:

```python
# FILE TYPE: Python Script (.py)
# WHAT IT DOES: Converts model evaluation metrics and predictions into a JSON file for the JS frontend.
# INPUTS: Trained models and evaluation data.
# OUTPUTS: `visualizer/data.json`

import json
import pandas as pd

def export_model_data():
    # 1. Gather your results (example mock data structure)
    model_results = {
        "metadata": {
            "project": "Canadian Wildfire Risk Predictor",
            "models_compared": ["Logistic Regression", "Random Forest", "Gradient Boosting"],
            "last_updated": "2026-06-06"
        },
        "performance_metrics": {
            "Random Forest": {"accuracy": 0.89, "precision": 0.84, "recall": 0.81, "f1_score": 0.82},
            "Gradient Boosting": {"accuracy": 0.92, "precision": 0.88, "recall": 0.85, "f1_score": 0.86},
            "Logistic Regression": {"accuracy": 0.76, "precision": 0.68, "recall": 0.65, "f1_score": 0.66}
        },
        "regional_risk_forecast": [
            {"region": "British Columbia - Zone 1", "current_risk": "High", "predicted_frp": 450.5, "cluster": 2},
            {"region": "Alberta - Zone 2", "current_risk": "Moderate", "predicted_frp": 210.2, "cluster": 1},
            {"region": "Ontario - Zone 3", "current_risk": "Low", "predicted_frp": 45.0, "cluster": 0}
        ]
    }

    # 2. Write dictionary directly to a JSON file inside your visualizer folder
    output_path = "../visualizer/data.json"
    with open(output_path, "w") as f:
        json.dump(model_results, f, indent=4)
    
    print(f"Successfully exported model results to {output_path}")

if __name__ == "__main__":
    export_model_data()

```

---

### 2. Example Structure of the Generated `data.json`

When Python runs that script, it produces a clean, readable JSON file that looks like this:

```json
{
    "metadata": {
        "project": "Canadian Wildfire Risk Predictor",
        "models_compared": ["Logistic Regression", "Random Forest", "Gradient Boosting"]
    },
    "performance_metrics": {
        "Gradient Boosting": {
            "accuracy": 0.92,
            "precision": 0.88,
            "recall": 0.85,
            "f1_score": 0.86
        }
    },
    "regional_risk_forecast": [
        {"region": "British Columbia - Zone 1", "current_risk": "High", "predicted_frp": 450.5}
    ]
}

```

---

### 3. Reading it in JavaScript (`visualizer/app.js`)

Because your frontend (`app.js`) runs in the browser, you can load this JSON file asynchronously using the native `fetch()` API:

```javascript
// FILE TYPE: JavaScript File (.js)
// WHAT IT DOES: Fetches the exported Python ML data and renders it into the HTML dashboard.

async function loadDashboard() {
    try {
        // Fetch the JSON file generated by Python
        const response = await fetch('data.json');
        const data = await response.json();

        console.log("Loaded Project Metadata:", data.metadata);

        // Example: Render model metrics dynamically into HTML elements
        const gbMetrics = data.performance_metrics["Gradient Boosting"];
        document.getElementById('accuracy-display').innerText = `${gbMetrics.accuracy * 100}%`;
        document.getElementById('f1-display').innerText = gbMetrics.f1_score;

        // Populate regional list
        renderRegions(data.regional_risk_forecast);

    } catch (error) {
        console.error("Error loading model data:", error);
    }
}

function renderRegions(regions) {
    const container = document.getElementById('region-list');
    container.innerHTML = regions.map(r => `
        <div class="card">
            <h3>${r.region}</h3>
            <p>Risk Level: <strong>${r.current_risk}</strong></p>
            <p>Predicted FRP: ${r.predicted_frp}</p>
        </div>
    `).join('');
}

document.addEventListener('DOMContentLoaded', loadDashboard);

```