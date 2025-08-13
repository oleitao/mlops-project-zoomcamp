# Course MLOps-Zoomcamp Final Project Capstone

## Project name

**Telecom customer churn prediction**

<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/Customer_churn_prediction.drawio.png">

## Project evaluation criteria:
https://github.com/DataTalksClub/mlops-zoomcamp/tree/main/07-project

## Project description

This project serves as the capstone assignment for the `mlops-zoomcamp` course offered by  [DataTalksClub](https://github.com/DataTalksClub/mlops-zoomcamp).

**Project Motivation**:
Predicting customer churn—identifying which users are at risk of discontinuing or canceling their service—is a crucial task for many organizations, particularly because retaining current customers is typically much less expensive than attracting new ones

In the telecommunications sector, where competition is fierce and customer attrition rates are high, accurately forecasting churn enables companies to proactively address potential losses and improve customer retention strategies

**Personal Context**:
My personal interest by data and AI as a Software Developer/DevOps. To merge my professional experience with my passion for analytics, I chose a **churn** prediction project for my final mlops-zoomcamp assignment.

**Project Overview**:
The main goal of this project is to deliver an online solution that predicts which customers are likely to leave the company. This involves developing a production-ready machine learning service that incorporates:

- Experiment tracking with **MLflow**

- Automated data pipelines using **Prefect**

- Monitoring and observability through **Evidently**, **Prometheus**, and **Grafana**

By combining these tools, the project aims to demonstrate best practices in deploying and managing machine learning models in a real-world setting, focusing on both technical robustness and business impact.




## Dataset

The dataset has been taken from [Kaggle](https://www.kaggle.com/competitions/customer-churn-prediction-2020/overview). 

See data dictionary: [Data_Dictionary](https://github.com/oleitao/mlops-project-zoomcamp/blob/main/dataset/README.md)


## Project preparations

This project is implemented on `Ubuntu 24.04` on AWS as below:

Platform details: Linux/UNIX

AMI name: ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server-20250610

Reproducibility are based on specific configuration above and already proved with `MacBook M4`

## Project structure

This repository is organized into two main directories along with a README.md file.

1. The dataset directory holds both the training and testing data sets.

2. The code directory features the primary application code and configuration files, including:

  - Dockerfile
  - docker-compose.yml
  - MLflow database setup
  - Prefect database setup
  - Prediction service modules
  - CI/CD pipeline scripts
  - Integration testing suite



## Project overview

The project began by running a training session using the "`model_training.py`" script to build the machine learning model. After training, the model was registered in the model registry through the "`model_registry.py`" script, and the model artifacts were saved in a "`Pickle`" file format. **MLflow** was utilized throughout this process for tracking experiments, managing the model registry, and storing artifacts, with all data being saved to the "`customer_chrun.db`" database

To automate and manage the machine learning workflow, **Prefect** was introduced as the orchestration tool. Prefect was responsible for deploying the project, defining tasks and flows, and scheduling the pipeline to execute at the designated times.

Following the training and deployment phases, the project still required further enhancements. The next step was to serve the trained model and ensure it met performance expectations in a user-facing application, which was to be developed using **Flask**.

Additionally, observability features were implemented to allow to monitor the service. This was achieved by integrating **Grafana**, **Prometheus**, and **Evidently**, which together provided real-time insights into model performance and detected data drift in the production environment. This setup enabled the operations team to quickly address any issues or collaborate with other teams to make informed decisions.


## Project instruction

### 1. Project repository

```bash
git clone https://github.com/oleitao/mlops-project-zoomcamp.git
```

Clone the project to the local server.

### 2. Work directory

```bash
cd ./mlops-project-zoomcamp/code
```

Move to the `./mlops-project-zoomcamp/code` from the current directory.

### 3. Build the services up

```bash
docker-compose up --build
```

This command will run the docker-compose to build up the services and dependencies for the services.

**NOTE**: Add `-d` to run in the detach mode


### 4. Environment preparation

```bash
python -m venv venv

(PS)
.\venv\Scripts\activate

```

This command will install all required dependencies and activtte the environment from this requisition of this project.

### 5. MLflow preparation

```bash
pip install mlflow

mlflow --version

mlflow ui -h 0.0.0.0 -p 5050 --backend-store-uri sqlite:///customer_churn.db --default-artifact-root ./mlruns
```

This command will build the mlflow service to work in this project e.g. `database` and `MLflow UI`.

**link**: http://localhost:5050/

**MLflow tracking**
<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/mlflow_tracking.png">

**MLflow registry**
<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/mlflow_registry.png">

**MLflow artifactts**
<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/mlflow_artifacts.png">


### 6. Prefect preparation

```bash
pip install 'prefect>=3.0.0'


prefect config set PREFECT_API_URL="http://localhost:4200/api" # local server

prefect server start --host localhost
```

The command above will set the `PREFECT API URL` at localhost with port 4200 and start `prefect server` 

**link**: http://0.0.0.0:4200/

**NOTE**: for use prefect as remote server need to set with the command:

```bash
prefect config set PREFECT_ORION_UI_API_URL="http://<external ip>:4200/api" # Remote server
```

**Prefect flow**
<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/prefect_flow.png">

**Prefect deployment**
<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/prefect_deployment.png">

**Prefect workqueue**
<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/prefect_workqueue.png">

**Prefect storage**
<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/prefect_storage.png">

### 7. MLflow Model training and monitoring

```bash
python model_training.py
```

Run python script `model_training.py` to start training the model. For this project use `LightGBMClassifier` to classification the data between customer who will **Churn** or  **Not Churn**.

In order to inspect the training process or details apart from command line. Open the `MLflow UI` or `Prefect UI` instead.

The model will transition to the `Staging` in the `MLflow UI` after finish training session.

### 8. MLflow Model registry and artifacts

```bash
python model_registry.py
```

Run `model_registry.py` to retrieve the data of the model from the `MLflow database` and compare current model in the production with the new model. If the new model is better then transition this new model to the production and give the archive stage to the production model.

### 9. Prefect deployment

```bash
prefect deployment create model_deployment.py
```

The command above will create the deployment from `model_deployment.py` and return the **deployment ID** in the terminal.

### 10. Prefect work-queue

```bash
prefect work-queue create -d <deployment ID> <work-queue name>
```

In order to see the deployment ID run the command:

```bash
prefect deployment ls
```

The work-queue should be created.

### 11. Prefect agent

```bash
prefect agent start <work-queue ID>
```

After created the work-queue. now the `Prefect agent` can work on the deployment to help the pipeline run in the specify time by schedule the deployment

In order to see the work-queue ID run the command:

```bash
prefect work-queue ls
```

**NOTE**: For the agent start API. It has sometimes the API gets stuck with no reason for me. So if get stuck with this part just wait and skip to the next step first. Then if finish all processes and back to the agent and the agent is still stuck trying to use the UI instead. The agent should be run normally.

### 12. Model prediction

Now the model ready to use after training and deploy to the production and the services should be ready to serve the prediction result of each customer who will **Churn** or **Not Churn** from our company.

In the current directory `(./mlops-project-zoomcamp/code)` run the command below to send the data to the model service and get the results.

```bash
python customer_data.py
```

### 13. Model & Data monitoring

In order to inspect the `data drift` or `model drift` see the **Grafana dashboard** to monitor then check if any data show abnormal or model performance become to bad.

<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/data_drift_report.png">

### 14. Service port

**All services port**
<img src="https://github.com/oleitao/mlops-project-zoomcamp/blob/main/images/service_port.png">


## Future improvement

This project still need more improvement to give more abilities for work with. 

1. Add the alert when the data drift or model performance is down.

2. When the alert occurs send some triggers. For example when the model performance is down show the alert to the operation team and send the trigger to the prefect to start the model deployment to re-train the model to get better model.

3. Combine both two above with CI/CD process. This will become to CI/CD/CT pipeline and deploy to the production automatically.

4. Add others model to training session and select the best model for the project.

5. Add model optimization such as `hyperopt` to tune hyperparameters.

6. Add IaC to manage and deploy the infrastructure and services.


## Tech Stack

- Flask
- MLflow
- Prefect
- Grafana
- Prometheus
- Evidently
- Mongodb
- Docker
