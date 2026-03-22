<div align="center">

  <h1>🎯 YT Sentiment Analysis</h1>

  <p>
    A Chrome extension that classifies YouTube comments as <strong>positive</strong>, <strong>negative</strong>, or <strong>neutral</strong> in real time — powered by a LightGBM classifier, a TF-IDF pipeline, and a production-grade MLOps backbone.
  </p>

  ![Python](https://img.shields.io/badge/Python-3.10-blue?logo=python)
  ![Flask](https://img.shields.io/badge/Flask-API-black?logo=flask)
  ![LightGBM](https://img.shields.io/badge/Model-LightGBM-green)
  ![Docker](https://img.shields.io/badge/Docker-containerized-blue?logo=docker)
  ![DVC](https://img.shields.io/badge/DVC-pipeline-945DD6?logo=dvc)
  ![MLflow](https://img.shields.io/badge/MLflow-experiment%20tracking-blue?logo=mlflow)
  ![CI/CD](https://img.shields.io/badge/CI%2FCD-GitHub%20Actions-black?logo=github-actions)
  ![AWS](https://img.shields.io/badge/Deploy-AWS%20CodeDeploy-orange?logo=amazon-aws)

</div>

---

## 📖 Overview

**YT Sentiment Analysis** is an end-to-end NLP project that analyses the sentiment of YouTube comments directly in your browser. A Chrome extension sends comment text to a Flask REST API, which runs inference using a trained **LightGBM** model backed by a **TF-IDF** vectorizer, and returns the sentiment label instantly.

The project is built with a fully reproducible MLOps pipeline — every stage from data ingestion to model registration is tracked, versioned, and automated.

---

## ✨ Features

- 🔌 **Chrome Extension** — Analyse YouTube comment sentiment without leaving the page
- ⚡ **LightGBM Classifier** — Fast, high-accuracy gradient boosting on TF-IDF features
- 🔤 **N-gram TF-IDF** — Captures unigrams, bigrams, and trigrams (range `[1, 3]`) with up to 10,000 features
- 🧹 **NLP Preprocessing** — NLTK-based stopword removal and lemmatisation via WordNet
- 🔁 **Reproducible DVC Pipeline** — 5-stage pipeline from raw data to registered model
- 📊 **MLflow Experiment Tracking** — All runs, metrics, and models logged and registered
- 🐳 **Docker Ready** — Containerised Flask API for consistent deployment
- ⚙️ **Configurable via `params.yaml`** — All hyperparameters in one place, tracked by DVC
- 🚀 **CI/CD to AWS** — Automated testing and deployment via GitHub Actions + CodeDeploy

---

## 🧠 How It Works

### ML Pipeline

```
Raw Data → Preprocessing → Model Training → Evaluation → Registration
```

1. **Data Ingestion** — Loads raw comment data and splits into train/test sets (80/20)
2. **Data Preprocessing** — Cleans text: lowercasing, stopword removal, lemmatisation
3. **Model Building** — Trains a LightGBM classifier on a TF-IDF matrix
4. **Model Evaluation** — Evaluates on the held-out test set, logs metrics to MLflow
5. **Model Registration** — Registers the best-performing model in the MLflow Model Registry

### Model Configuration (`params.yaml`)

```yaml
data_ingestion:
  test_size: 0.20

model_building:
  ngram_range: [1, 3]
  max_features: 10000
  learning_rate: 0.09
  max_depth: 20
  n_estimators: 367
```

### Chrome Extension + API Flow

```
YouTube Page → Chrome Extension → POST /predict → Flask API → LightGBM → Sentiment Label
```

---

## 🗂️ Project Structure

```
yt-sentiment-analysis/
├── flask_app/                    # Flask REST API & Chrome extension files
│
├── src/
│   ├── data/
│   │   ├── data_ingestion.py     # Load & split raw data
│   │   └── data_preprocessing.py # NLP cleaning & feature prep
│   ├── model/
│   │   ├── model_building.py     # Train LightGBM + TF-IDF
│   │   ├── model_evaluation.py   # Evaluate & log to MLflow
│   │   └── register_model.py     # Register best model in MLflow
│   └── visualization/
│       └── visualize.py
│
├── data/
│   ├── raw/                      # Original train/test splits (DVC-tracked)
│   └── interim/                  # Preprocessed CSVs (DVC-tracked)
│
├── models/                       # Serialised model artefacts
├── notebooks/                    # EDA and experimentation
├── reports/figures/              # Evaluation plots & confusion matrices
├── docs/                         # Sphinx documentation
├── deploy/scripts/               # AWS CodeDeploy shell scripts
│
├── Dockerfile                    # Container for Flask API
├── appspec.yml                   # AWS CodeDeploy configuration
├── dvc.yaml                      # DVC pipeline definition
├── dvc.lock                      # Locked pipeline state
├── params.yaml                   # Hyperparameter configuration
├── Makefile                      # Convenience commands
├── requirements.txt              # Python dependencies
└── setup.py                      # Package installation
```

---

## 🚀 Getting Started

### Prerequisites

- Python 3.10+
- Docker (optional)
- DVC (for data pipeline)
- MLflow (for experiment tracking)

### 1. Clone the Repository

```bash
git clone https://github.com/BayesianDecoder/yt-sentiment-analysis.git
cd yt-sentiment-analysis
```

### 2. Install Dependencies

```bash
pip install -r requirements.txt
pip install -e .
```

### 3. Pull Data & Run the Pipeline

```bash
dvc pull        # Pull tracked data from remote storage
dvc repro       # Reproduce the full pipeline end-to-end
```

Or run individual stages:

```bash
make data       # Data ingestion + preprocessing
make train      # Model training
```

### 4. Start the Flask API

```bash
cd flask_app
python app.py
```

The API will be available at `http://localhost:5000`.

---

## 🐳 Docker

### Build the Image

```bash
docker build -t yt-sentiment-api .
```

### Run the Container

```bash
docker run -p 5000:5000 yt-sentiment-api
```

---

## 🔌 Chrome Extension

1. Open Chrome and go to `chrome://extensions/`
2. Enable **Developer Mode** (top right)
3. Click **Load unpacked** and select the `flask_app/` directory
4. Navigate to any YouTube video and click the extension icon to analyse comments

> Make sure the Flask API is running locally or pointed to your deployed instance.

---

## 📊 Model Performance

A confusion matrix on the test set is included in the repository root (`confusion_matrix_Test Data.png`). Detailed metrics for all experiment runs are tracked in **MLflow** and can be viewed by running:

```bash
mlflow ui
```

---

## ⚙️ CI/CD Pipeline

The GitHub Actions workflow (`.github/workflows/`) automatically:

1. Runs tests and environment checks on every push
2. Builds the Docker image
3. Deploys to AWS via **CodeDeploy** using `appspec.yml` and the scripts in `deploy/scripts/`

---

## 📦 Key Dependencies

| Library | Purpose |
|---|---|
| `lightgbm` | Gradient boosting classifier |
| `scikit-learn` | TF-IDF vectoriser, metrics |
| `nltk` | Text preprocessing (stopwords, lemmatisation) |
| `flask` | REST API serving predictions |
| `mlflow` | Experiment tracking & model registry |
| `dvc` + `dvc-s3` | Pipeline & data versioning |
| `pandas` / `numpy` | Data manipulation |

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m 'Add your feature'`
4. Push and open a Pull Request

---

## 📄 License

This project is licensed under the terms in the [LICENSE](LICENSE) file.

---

<div align="center">
  Made by <a href="https://github.com/BayesianDecoder">BayesianDecoder</a>
</div>
