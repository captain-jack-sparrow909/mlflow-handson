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


# production ready setup using PostgreSQL and kubernetes:

## steps:

### 1. setting up PostreSQL instance on AWS RDS:
    to make the data persistent, as for just Sqlite, it can get lost if kubernetes pod crashed
    if you're not exposing this DB through EC2 or Lambda then you need to make this publically available in network and connectivity, 
    otherwise it won't connect.

### 2. setting up a dedicated mlflow db and user on PostreSQL:
    to have things properly organized, download PostgreSQL client or brew install psql
    and then run -> psql -h host-link-from-aws -p 5432 -U postgres

    once DB is connected:
    creating DB and user access:
        ``` CREATE DATABASE mlflow;
            CREATE USER mlflow_user WITH PASSWORD 'your_secure_password';
            GRANT ALL PRIVILEGES ON DATABASE mlflow TO mlflow_user;
            GRANT ALL PRIVILEGES ON SCHEMA public TO mlflow_user;
        ```
    

### 3. running mlflow server on kubernetes cluster and be able to reach the PostgreSQL DB:
    data should go from the ML Flow to the DB

    kind create cluster --name=production-mlflow-setup 

    if not already, then below step is needed once:
    helm repo add community-charts https://community-charts.github.io/helm-charts
    helm repo update

    creating namespace:
    kubectl create ns mlflow       used in below command

    and then, make necessary changes:

    helm install mlflow community-charts/mlflow \
    --namespace mlflow \
    --set backendStore.databaseMigration=true \
    --set backendStore.postgres.enabled=true \
    --set backendStore.postgres.host=your-postgres-host \
    --set backendStore.postgres.port=5432 \
    --set backendStore.postgres.database=mlflow \
    --set backendStore.postgres.user=mlflow_user \
    --set backendStore.postgres.password=your_secure_password


    lastly, port forwarding:
    kubectl -n mlflow port-forward pod/podName 7004:5000 --address 0.0.0.0
