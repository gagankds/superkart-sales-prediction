# SuperKart Sales Prediction and Deployment

## Overview
This project focuses on building a robust sales forecasting solution for SuperKart, a retail chain. The solution includes an end-to-end Machine Learning pipeline, from Exploratory Data Analysis (EDA) and model building to deployment of a Flask-based backend API and a Streamlit-based frontend application. The entire system is containerized using Docker and deployed using GitHub Codespaces for scalable and efficient operations.

## Features
- **Data Ingestion & Preprocessing**: Handles raw sales data, cleans it, and engineers relevant features like `Product_Id_char`, `Store_Age_Years`, and `Product_Type_Category`.
- **Exploratory Data Analysis (EDA)**: Comprehensive analysis to understand data distribution, relationships, and key revenue drivers.
- **Model Training**: Develops and evaluates multiple regression models, including Decision Trees and XGBoost Regressors.
- **Hyperparameter Tuning**: Optimizes model performance using `GridSearchCV`.
- **Model Serialization**: Saves the best-performing model (XGBoost Regressor) for deployment.
- **Flask Backend API**: A lightweight Python Flask application (`app.py`) that serves the trained ML model for real-time (online) and batch predictions.
- **Streamlit Frontend UI**: An interactive web application (`app.py`) built with Streamlit, allowing users to input product and store details for sales predictions or upload CSV files for batch predictions.
- **Containerization (Docker)**: Both the Flask backend and Streamlit frontend are containerized using Dockerfiles for consistent and isolated environments.
- **GitHub Codespaces Deployment**: The entire solution is designed for seamless deployment within GitHub Codespaces, leveraging Docker Compose for multi-container orchestration.

## Deployed Application Architecture
The application consists of two main components, each running in its own Docker container:
1.  **Backend (Flask API)**: Exposed on port `7860`.
2.  **Frontend (Streamlit UI)**: Exposed on port `8501`.

These containers communicate over a Docker network, allowing the Streamlit frontend to send prediction requests to the Flask backend.

## Project Structure
```
. # Root directory
├── backend_files/
│   ├── app.py              # Flask application for ML model serving
│   ├── requirements.txt    # Python dependencies for the backend
│   ├── Dockerfile          # Dockerfile for the Flask backend
│   └── superkart_model.joblib # Serialized XGBoost model
├── frontend_files/
│   ├── app.py              # Streamlit application for user interface
│   ├── requirements.txt    # Python dependencies for the frontend
│   └── Dockerfile          # Dockerfile for the Streamlit frontend
├── SuperKart.csv           # Original dataset
├── Batch_Data_SuperKart.csv # Sample batch data for prediction
└── README.md               # This file
```

## Setup and Run Locally

### Prerequisites
- Docker Desktop installed
- Python 3.9+

### Steps
1.  **Clone the repository**:
    ```bash
    git clone https://github.com/your-username/superkart-sales-prediction.git
    cd superkart-sales-prediction
    ```
2.  **Build and Run Docker Containers** (using `docker-compose` if available, otherwise manually):
    Navigate to the project root and build the Docker images:
    ```bash
    docker build -t superkart-backend -f backend_files/Dockerfile .
    docker build -t superkart-frontend -f frontend_files/Dockerfile .
    ```
    Then, run the containers, ensuring they are on the same network:
    ```bash
    docker network create superkart_network
    docker run -d --name superkart_backend --network superkart_network -p 7860:7860 superkart-backend
    docker run -d --name superkart_frontend --network superkart_network -p 8501:8501 superkart-frontend
    ```
    *Note: The `app.py` in `frontend_files` is configured to use `http://backend:7860` to communicate with the Flask API, assuming a Docker network service name 'backend'. If running manually as above, ensure the `BACKEND_URL` in `frontend_files/app.py` is updated to `http://localhost:7860` for local testing.*

3.  **Access the Frontend**: Open your web browser and navigate to `http://localhost:8501`.

## API Endpoints
### Backend (Flask API)
- **`/` (GET)**: Welcome message.
- **`/v1/predict` (POST)**: Predicts sales for a single product. Expects a JSON payload with product features.
- **`/v1/predictbatch` (POST)**: Predicts sales for a batch of products. Expects a CSV file upload.

### Frontend (Streamlit UI)
- Provides an interactive interface for single and batch predictions, communicating with the Flask backend.

## Model Details
- **Model Type**: XGBoost Regressor (untuned for better generalization).
- **Features Used**: `Product_Weight`, `Product_Sugar_Content`, `Product_Allocated_Area`, `Product_MRP`, `Store_Size`, `Store_Location_City_Type`, `Store_Type`, `Product_Id_char`, `Store_Age_Years`, `Product_Type_Category`.
- **Serialization**: `joblib` is used to save and load the trained model along with its preprocessing pipeline.

## Actionable Insights
1.  **Prioritize High-Impact Categories**: `Fruits and Vegetables` and `Snack Foods` are key revenue drivers, especially in top stores like `OUT004` and `OUT003`. Focus on stocking and promotions for these.
2.  **Leverage 'Low Sugar' Demand**: Capitalize on consumer health consciousness by expanding and promoting low-sugar options, which are significant revenue contributors.
3.  **Replicate `OUT004`'s Success**: `OUT004` is the highest-performing store. Analyze its operational strategies, product mix, and marketing to apply lessons learned to other stores.
4.  **Strategic Review of `OUT002`**: `OUT002` (Small Food Mart in Tier 3 city) is the lowest performer. Evaluate its product mix, pricing, and market demand for optimization.
5.  **Pricing & Weight Strategy**: Higher-priced and heavier products (`Product_MRP`, `Product_Weight`) correlate with higher sales. Consider introducing more premium or bulk options.
6.  **Store Size Optimization**: `Medium` and `High` stores show better sales performance. Optimize space in smaller stores or explore expansion opportunities.
7.  **Location-Based Strategies**: `Tier 2` cities are the most profitable. Invest further in these and tailor strategies for `Tier 1` and `Tier 3` cities.
8.  **Data-Driven Decision Making**: Integrate the deployed model for real-time/batch forecasting to aid inventory, promotions, and resource allocation decisions.

## Technologies Used
-   **Python**: Core programming language.
-   **Pandas**: Data manipulation and analysis.
-   **Scikit-learn**: Machine learning framework.
-   **XGBoost**: Gradient Boosting library for model training.
-   **Flask**: Web framework for building the backend API.
-   **Streamlit**: Framework for building the interactive frontend UI.
-   **Docker**: Containerization of applications.
-   **GitHub Codespaces**: Cloud-based development environment and deployment platform.
-   **Joblib**: Model serialization.

"""

