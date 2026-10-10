# Mansik Santulan Score (मानसिक संतुलन)

> Machine Learning system predicting student mental health balance scores based on daily digital habits, academic workload, and lifestyle factors.

[![Python](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![FastAPI](https://img.shields.io/badge/FastAPI-0.110%2B-009688.svg)](https://fastapi.tiangolo.com/)
[![Scikit-Learn](https://img.shields.io/badge/scikit--learn-1.4%2B-F7931E.svg)](https://scikit-learn.org/)
[![Render](https://img.shields.io/badge/Deployment-Render-46E3B7.svg)](https://mansik-santulan-score.onrender.com)

---

## 📌 Project Overview

**Mansik Santulan Score** is an end-to-end machine learning project designed to quantify the impact of social media usage, smartphone screen time, sleep patterns, study hours, and stress levels on a student's overall mental well-being.

The model outputs a **Mental Health Score on a scale from 1.0 to 10.0**:
- **8.0 – 10.0 (Thriving)**: Healthy balance between screen time, physical activity, and sleep.
- **6.0 – 7.9 (Stable)**: Moderate routine with manageable daily pressure.
- **4.0 – 5.9 (At Risk)**: Elevated screentime, sleep deficit, or compounding stress.
- **1.0 – 3.9 (High Distress)**: Severe lifestyle imbalance requiring immediate intervention and care.

> **Note**: This application is built as an educational and analytical demonstration modeled from survey patterns. It is **not** a clinical diagnostic tool or medical substitute.

---

## 🏗️ System Architecture

The project consists of three integrated layers:

1. **Machine Learning Pipeline**:
   - Built with Scikit-Learn's `Pipeline` and `ColumnTransformer`.
   - Continuous numerical features scaled using `StandardScaler`.
   - High-cardinality country features grouped into top 10 categories + "Other".
   - Categorical variables one-hot encoded using `OneHotEncoder(handle_unknown='ignore')`.
   - Evaluated across Linear Regression and Random Forest Regressor models with 5-fold cross-validation.
   - Serialized as `Mental_Health_Model.pkl` via Joblib.

2. **FastAPI Backend (`main.py`)**:
   - Pydantic models for strict payload validation (`StudentData` and `PredictionResponse`).
   - CORS middleware enabled for cross-origin frontend requests.
   - Fallback country grouping logic ensuring robust predictions even for unobserved inputs.
   - Fast inference (<15ms per request).

3. **Frontend Dashboard (`index.html`, `style.css`, `script.js`)**:
   - Clean, modern dark-themed interface built with vanilla semantic HTML, CSS, and modern JavaScript.
   - Responsive multi-column layout with real-time field validation.
   - Dynamic SVG half-circle gauge visualizing scores with color-coded wellness tiers.
   - Segmented control for intuitive stress level selection.

---

## 📊 Dataset & Features

The model is trained on a comprehensive student survey dataset (`Student Social Media And Mental Health Impact.csv`) containing **5,000 student records**.

### Input Features

| Feature Name | Type | Description / Range |
| :--- | :--- | :--- |
| `Age` | Numerical | Student age (10 – 100) |
| `Gender` | Categorical | `Male`, `Female` |
| `Country` | Categorical | Country of residence (India, USA, Canada, UK, Australia, Germany, Mexico, Turkey, France, Other) |
| `Academic_Level` | Categorical | `High School`, `Undergraduate`, `Graduate` |
| `Most_Used_Platform` | Categorical | Instagram, YouTube, TikTok, WhatsApp, Snapchat, Twitter, LinkedIn, etc. |
| `Purpose_Of_Use` | Categorical | `Entertainment`, `Education`, `Networking`, `News` |
| `Avg_Daily_Usage_Hours` | Numerical | Daily social media hours (0 – 24) |
| `Daily_Unlocks` | Numerical | Daily smartphone unlock count |
| `Study_Hours` | Numerical | Productive academic study hours per day (0 – 24) |
| `Physical_Activity_Hours` | Numerical | Exercise and sports hours per day (0 – 24) |
| `Sleep_Hours_Per_Night` | Numerical | Average sleep duration per night (0 – 24) |
| `Stress_Level` | Categorical | `Low`, `Medium`, `High`, `Very High` |

### Target Variable
- `Mental_Health_Score`: Continuous target ranging from **1.0** to **10.0**.

---

## 📈 Model Performance & Comparison

Multiple regression models were trained and benchmarked on an 80/20 train-test split:

| Model | Training R² | Testing R² | MAE | RMSE | Notes |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Linear Regression** | 0.7237 | 0.7398 | 0.5362 | 0.6760 | Linear baseline |
| **Random Forest (Default)** | 0.9808 | **0.8776** | **0.3472** | **0.4637** | Strongest predictive capability |
| **Random Forest (Tuned)** | 0.9547 | 0.8650 | 0.3689 | 0.4869 | 5-fold RandomizedSearchCV |

The Random Forest model effectively captures non-linear interactions—such as the compounding negative impact when low sleep (<5 hours) coincides with very high screen time (>7 hours) and high unlocks.

---

## 🗂️ Project Structure

```
Mental-Health-Score/
├── Student Social Media And Mental Health Impact.csv   # Raw survey dataset (5,000 rows)
├── ML_Project.ipynb                                   # Jupyter notebook (EDA, preprocessing, modeling)
├── ML Project.html                                    # Exported HTML notebook report
├── Mental_Health_Model.pkl                            # Serialized production pipeline
├── main.py                                            # FastAPI application
├── requirements.txt                                   # Python dependencies
├── index.html                                         # Web interface
├── style.css                                          # Modern styling and SVG gauge animations
├── script.js                                          # Client-side validation & API integration
└── README.md                                          # Project documentation
```

---

## 🚀 Getting Started

Follow these steps to run the project locally on your machine.

### Prerequisites
- Python 3.9 or higher
- Git
- Modern web browser

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/Mental-Health-Score.git
cd Mental-Health-Score
```

### 2. Set Up Virtual Environment
On Windows (PowerShell):
```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

On macOS / Linux:
```bash
python3 -m venv venv
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Start the FastAPI Server
```bash
uvicorn main:app --reload --port 8000
```
The API documentation (Swagger UI) will be accessible at [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs).

### 5. Launch the Frontend
Open `index.html` directly in your browser, or serve it using VS Code Live Server or Python's built-in HTTP server:
```bash
python -m http.server 3000
```
Then navigate to [http://127.0.0.1:3000](http://127.0.0.1:3000).

---

## 📡 API Reference

### Health Check
```http
GET /
```
**Response:**
```json
{
  "Welcome to Sheryians AI School Guys"
}
```

---

### Predict Mental Health Score
```http
POST /predict
Content-Type: application/json
```

#### Request Body
```json
{
  "age": 21,
  "gender": "Female",
  "country": "India",
  "academic_level": "Undergraduate",
  "most_used_platform": "Instagram",
  "purpose_of_use": "Entertainment",
  "avg_daily_usage_hours": 4.5,
  "daily_unlocks": 85,
  "study_hours": 5.0,
  "physical_activity_hours": 1.0,
  "sleep_hours_per_night": 7.0,
  "stress_level": "Medium"
}
```

#### Response Body
```json
{
  "predicted_mental_health_score": 6.82
}
```

---

## 🌐 Live Demo & Deployment

The backend is deployed on **Render**:
- **API Endpoint**: `https://mansik-santulan-score.onrender.com`
- **Predict Endpoint**: `https://mansik-santulan-score.onrender.com/predict`

The frontend communicates with this backend seamlessly and displays the computed score, visual gauge progress, and wellness tier analysis.

---

## 📄 License & Attribution

This project is developed for educational and portfolio demonstration purposes as part of the AI & Machine Learning coursework.
