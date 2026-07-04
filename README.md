## Steps:

### 1. environment setup:
    python3.12 -m venv .venv
    source .venv/bin/activate

### 2. install mlflow:
    pip install mlflow

### 3. configuring mlflow:
    mlflow ui --backend-store-uri sqlite:///mlflow.db --port 7006     sqlite is built into python3, no need separate installation

### 4. go to browser:
    http://localhost:7006 mlflow ui should be there


## Additional:
    mlflow docs: https://community-charts.github.io/docs/charts/mlflow/basic-installation
    
