# 🎓 Student Performance Predictor

An end-to-end Machine Learning project that predicts a student's **Math score** based on demographic and academic factors such as gender, ethnicity, parental education, lunch type, test preparation, and reading/writing scores. The project follows a modular ML pipeline architecture and is deployed as a Flask web app on **AWS Elastic Beanstalk**.

---

## 📌 Problem Statement

This project explores how a student's exam performance is influenced by variables such as:

- Gender
- Race/Ethnicity
- Parental level of education
- Lunch type (standard / free-reduced)
- Test preparation course completion

The goal is to train a regression model that predicts the **Math score** of a student, given the above features along with their Reading and Writing scores.

**Dataset source:** [Students Performance in Exams — Kaggle](https://www.kaggle.com/datasets/spscientist/students-performance-in-exams) (1000 rows × 8 columns)

---

## 🧠 ML Pipeline Architecture

The project is structured as a modular, production-style ML pipeline:

```
Raw Data → Data Ingestion → Data Transformation → Model Training → Model Selection → Prediction Pipeline → Flask App
```

| Stage | Component | Description |
|---|---|---|
| 1 | `src/components/data_ingestion.py` | Reads the raw dataset, splits it into train/test sets, and saves them as CSV artifacts |
| 2 | `src/components/data_transformation.py` | Builds preprocessing pipelines (imputation, scaling, one-hot encoding) using `ColumnTransformer` and saves the fitted preprocessor as a `.pkl` artifact |
| 3 | `src/components/model_trainer.py` | Trains multiple regression algorithms, tunes hyperparameters via `GridSearchCV`, and selects the best-performing model based on R² score |
| 4 | `src/pipeline/predict_pipeline.py` | Loads the saved model + preprocessor and serves predictions for new input data |
| 5 | `application.py` | Flask app exposing a web form to collect student details and display the predicted Math score |

Custom **logging** (`src/logger.py`) and **exception handling** (`src/exception.py`) modules are used throughout the pipeline for traceability and debugging.

---

## 🤖 Models Evaluated

During experimentation (see `notebook/2. MODEL TRAINING.ipynb`), the following regression algorithms were evaluated and compared on R² score:

| Model | R² Score (Test) |
|---|---|
| **Ridge Regression** | **0.8806** |
| Linear Regression | 0.8803 |
| CatBoost Regressor | 0.8516 |
| AdaBoost Regressor | 0.8498 |
| Random Forest Regressor | 0.8473 |
| Lasso Regression | 0.8253 |
| XGBRegressor | 0.8216 |
| K-Neighbors Regressor | 0.7838 |
| Decision Tree | 0.7603 |

The production pipeline (`src/components/model_trainer.py`) further tunes Random Forest, Decision Tree, Gradient Boosting, Linear Regression, XGBoost, CatBoost, and AdaBoost using `GridSearchCV`, and automatically persists the best-performing model to `artifacts/model.pkl`.

---

## 📂 Project Structure

```
aws-student-performance-predictor/
├── .ebextensions/              # AWS Elastic Beanstalk configuration
│   ├── 01_packages.config      # System-level packages (gcc, openssl-devel, etc.)
│   ├── pip.config              # Installs dependencies from requirements.txt
│   └── python.config           # WSGI entry point configuration
├── artifacts/                  # Saved model & preprocessor objects
│   ├── model.pkl
│   └── preprocessor.pkl
├── notebook/
│   ├── 1 . EDA STUDENT PERFORMANCE .ipynb   # Exploratory Data Analysis
│   ├── 2. MODEL TRAINING.ipynb              # Model training & comparison
│   └── data/stud.csv                        # Raw dataset
├── src/
│   ├── components/
│   │   ├── data_ingestion.py
│   │   ├── data_transformation.py
│   │   └── model_trainer.py
│   ├── pipeline/
│   │   ├── predict_pipeline.py
│   │   └── train_pipeline.py
│   ├── exception.py
│   ├── logger.py
│   └── utils.py
├── templates/
│   ├── home.html               # Prediction form
│   └── index.html              # Landing page
├── application.py              # Flask app entry point
├── wsgi.py                     # WSGI entry point for AWS EB
├── Procfile                    # Gunicorn start command
├── requirements.txt
├── setup.py
└── README.md
```

---

## 🛠️ Tech Stack

- **Language:** Python
- **Web Framework:** Flask
- **ML Libraries:** scikit-learn, CatBoost, XGBoost
- **Data Handling:** Pandas, NumPy
- **Serialization:** Pickle / Dill
- **Deployment:** AWS Elastic Beanstalk, Gunicorn
- **Notebook/EDA:** Jupyter Notebook, Matplotlib, Seaborn

---

## ⚙️ Installation & Local Setup

1. **Clone the repository**
   ```bash
   git clone https://github.com/Gayathri-Reddy874/aws-student-performance-predictor.git
   cd aws-student-performance-predictor
   ```

2. **Create a virtual environment** (recommended)
   ```bash
   python -m venv venv
   source venv/bin/activate    # On Windows: venv\Scripts\activate
   ```

3. **Install dependencies**
   ```bash
   pip install -r requirements.txt
   ```

4. **(Optional) Re-train the model**
   ```bash
   python src/components/data_ingestion.py
   ```
   This runs the full pipeline: data ingestion → transformation → model training, and regenerates the artifacts in `artifacts/`.

5. **Run the Flask app**
   ```bash
   python application.py
   ```

6. Open your browser and navigate to:
   ```
   http://127.0.0.1:5000/predictdata
   ```

---

## 🌐 Usage

1. Visit the `/predictdata` route.
2. Fill in the form with:
   - Gender
   - Race/Ethnicity
   - Parental level of education
   - Lunch type
   - Test preparation course status
   - Reading score
   - Writing score
3. Click **Predict your Maths Score** to get the predicted Math score.

---

## ☁️ Deployment (AWS Elastic Beanstalk)

This project is configured for deployment on **AWS Elastic Beanstalk** using:

- `Procfile` → tells Elastic Beanstalk to run the app with Gunicorn (`gunicorn application:application`)
- `wsgi.py` → WSGI entry point
- `.ebextensions/` → installs system packages (gcc, openssl-devel, etc.) and Python dependencies, and configures the WSGI path

**Deployment steps (high level):**

1. Install the [EB CLI](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/eb-cli3.html).
2. Initialize the Elastic Beanstalk application:
   ```bash
   eb init
   ```
3. Create and deploy an environment:
   ```bash
   eb create student-performance-env
   ```
4. Open the deployed app:
   ```bash
   eb open
   ```

---

## 📈 Future Improvements

- Add CI/CD pipeline (e.g., GitHub Actions) for automated testing and deployment
- Containerize the app with Docker
- Add unit tests for pipeline components
- Improve UI/UX of the prediction form
- Add model monitoring and retraining triggers

---

## 👩‍💻 Author

**Mallareddygari Gayathri**
GitHub: [@Gayathri-Reddy874](https://github.com/Gayathri-Reddy874)

---

## 📄 License

This project is open-sourced for educational purposes. Feel free to fork and build upon it.
