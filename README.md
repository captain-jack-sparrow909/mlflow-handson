# Non prod setup:

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


# Non prod setup using kubernetes:

## Steps:

### 1. setting up kubernetes cluster, you can use any way you like of creating k8s cluster
    kind create cluster --name=basic-mlflow-cluster     
    here we're using kind, you need to have docker running
    if you don't have kind installed: brew install kind

### 2. running some helm commands: to add community charts repo and to be able to pull mlflow chart
    helm repo add community-charts https://community-charts.github.io/helm-charts
    helm repo update

    helm install mlflow-community community-charts/mlflow

### 3. check status of your pods:
    kubectl get pods

### 4. once it's running state, run port forwarding command below:
    kubectl port-forward pod/podName 7006:500 --address 0.0.0.0
    get podName from above step
    7006 -> port number is upto you
    5000 -> default port on which mlflow run
    0.0.0.0 -> binding the port to the localhost

