```markdown
# 🚗 Vehicle Performance & Analytics MLOps Pipeline

An end-to-end, production-grade MLOps pipeline designed with a modular architecture to automate the data pipeline, model training, evaluation, and containerized deployment. This project leverages modern enterprise infrastructure tools including AWS services, MongoDB Atlas, Docker, and GitHub Actions to form a fully continuous integration and continuous deployment (CI/CD) ecosystem.

---

## 🏗️ System Architecture & Workflow

The architecture is built cleanly around decoupled, modular components:

```text
  [ Data Source: MongoDB Atlas ]
                 │
                 ▼
       ┌───────────────────┐
       │   Data Ingestion  │ ──► Drops artifacts locally
       └───────────────────┘
                 │
                 ▼
       ┌───────────────────┐
       │  Data Validation  │ ──► Validates dataset against schema.yaml
       └───────────────────┘
                 │
                 ▼
       ┌───────────────────┐
       │Data Transformation│ ──► Handles preprocessing & feature engineering
       └───────────────────┘
                 │
                 ▼
       ┌───────────────────┐
       │   Model Trainer   │ ──► Trains ML models & outputs estimators
       └───────────────────┘
                 │
                 ▼
       ┌───────────────────┐
       │ Model Evaluation  │ ──► Evaluates against AWS S3 Production Baseline
       └───────────────────┘
                 │ (If performance increases by Delta >= 0.02)
                 ▼
       ┌───────────────────┐
       │   Model Pusher    │ ──► Pushes validated model to AWS S3 Registry
       └───────────────────┘
                 │
                 ▼
       ┌───────────────────┐
       │ Production Serving│ ──► Exposed via app.py (Port 5080) on AWS EC2
       └───────────────────┘

```

---

## 🛠️ Technology Stack Matrix

| Layer | Tools & Technologies |
| --- | --- |
| **Core Language / Env** | Python 3.10, Conda, Windows Powershell / Linux Bash |
| **NoSQL Database** | MongoDB Atlas (Cloud Database Cluster) |
| **Cloud Infrastructure** | AWS IAM, AWS S3 (Model Registry), AWS ECR (Container Registry), AWS EC2 (Ubuntu 24.04 Production Instance) |
| **DevOps & CI/CD** | Docker, GitHub Actions (Self-Hosted Runner Integration) |
| **Web Server Layer** | Flask/FastAPI (`app.py`), HTML5/CSS3 Templates |

---

## ⚡ Key Engineering Highlights

* **Modular Structure Architecture:** Built entirely using decoupled components (`DataIngestion`, `DataValidation`, `DataTransformation`, `ModelTrainer`, `ModelEvaluation`, `ModelPusher`) communicating via immutable entities (`config_entity.py`, `artifact_entity.py`).
* **Robust Production Operations:** Custom global Logging and Exception Monitoring engines ensure absolute system traceability during workflow executions.
* **Strict Schema Verification:** Real-time data verification pipeline powered by `schema.yaml` validations checking data structures before feeding downstream tasks.
* **Dynamic Model Registry Gatekeeping:** Automated validation engine checks production models stored on AWS S3, requiring a hard baseline accuracy improvement threshold ($\Delta \ge 0.02$) to push upgrades.
* **Self-Hosted Infrastructure Delivery:** Custom continuous delivery runner hosted straight on an AWS EC2 instance executing automated Docker builds on push actions.

---

## 🚀 Production Deployment Guide

### Pillar 1: Local Development Environment Setup

1. **Scaffold Directory Workspace:** Initialize the modular project layout configuration:
```bash
python template.py

```


2. **Local Package Management:** Structure your local configurations within `setup.py` and `pyproject.toml` to register sub-packages seamlessly.
3. **Isolate Virtual Environment:** Spin up and activate an isolated Conda environment:
```bash
conda create -n vehicle python=3.10 -y
conda activate vehicle

```


4. **Install System Dependencies:** Install essential packages and verify package compilation:
```bash
pip install -r requirements.txt
pip list

```



### Pillar 2: NoSQL Data Layer Integration (MongoDB Atlas)

1. **Provision Database Cluster:** * Sign up for **MongoDB Atlas**, initialize a project, and choose an **M0 Free Tier** deployment cluster.
* Set your custom database admin username and secure password credentials.


2. **Network Whitelisting:** Navigate to **Network Access**, choose *Add IP Address*, and insert `0.0.0.0/0` to allow global connectivity.
3. **Extract Drivers URI:** Navigate to Database Connection Drivers -> Select **Python (v3.6+)** and copy your direct connection URI string.
4. **Data Seeding Workspace:** Spin up a workspace script under `notebook/mongoDB_demo.ipynb` using your active `vehicle` kernel environment to seed dataset records dynamically down to the Atlas NoSQL collections.

### Pillar 3: Pipeline Initialization & Component Architecture

1. **Inject Global Constants:** Define constants securely across `constants.__init__.py` and establish database connections via `configuration.mongo_db_connections.py`.
2. **Initialize Environment Credentials:**
* **Linux/macOS Bash:**
```bash
export MONGODB_URL="mongodb+srv://<username>:<password>@cluster.mongodb.net/..."
echo $MONGODB_URL

```


* **Windows PowerShell:**
```powershell
$env:MONGODB_URL="mongodb+srv://<username>:<password>@cluster.mongodb.net/..."
echo $env:MONGODB_URL

```




3. **Execute Engine Verification:** Run pipeline runs using testing harnesses to verify operational logging, custom errors, and local ingestions:
```bash
python demo.py

```



### Pillar 4: Enterprise AWS Cloud Infrastructure Configuration

1. **IAM Identity Provisioning:** Create an AWS user named `firstproj`, attach `AdministratorAccess`, and generate your explicit CLI Access Keys.
2. **Export Infrastructure Context Tokens:**
```bash
# Bash Env Initialization
export AWS_ACCESS_KEY_ID="AIZAIOSFODNN7EXAMPLE"
export AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"

# PowerShell Env Initialization
$env:AWS_ACCESS_KEY_ID="AIZAIOSFODNN7EXAMPLE"
$env:AWS_SECRET_ACCESS_KEY="wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY"

```


3. **Register S3 Production Model Bucket:** Spin up an AWS S3 Bucket named `my-model-mlopsproj` within `us-east-1` to act as an un-blocked model registry checkpoint workspace. Ensure configuration parameters are matched inside `constants.__init__.py`:
```python
MODEL_EVALUATION_CHANGED_THRESHOLD_SCORE: float = 0.02
MODEL_BUCKET_NAME = "my-model-mlopsproj"
MODEL_PUSHER_S3_KEY = "model-registry"

```



### Pillar 5: Continuous Integration & Deployment (CI/CD Engine)

1. **Dockerize Project Runtime:** Configure your production runtime environments cleanly via local `Dockerfile` and `.dockerignore` files.
2. **Setup Container Registries (ECR):** Provision a private AWS Elastic Container Repository labeled `vehicleproj` inside `us-east-1` and note its URI.
3. **Provision Hosting Server (EC2):** Spin up an Ubuntu 24.04 Long Term Support `T2.Medium` server instance using a storage allocation blueprint of 30 GB.
4. **Install Docker Engine on EC2 Remote Instance:**
```bash
sudo apt-get update -y && sudo apt-get upgrade -y
curl -fsSL [https://get.docker.com](https://get.docker.com) -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker ubuntu && newgrp docker

```


5. **Attach GitHub Runner Security Matrix:**
* Navigate to GitHub Repository Settings -> **Actions** -> **Runners** -> **New Self-Hosted Runner**.
* Execute the configuration download and setup payloads straight inside the remote target EC2 session. Initialize it using:
```bash
./run.sh

```




6. **Register Encrypted Action Secrets:** Store your operational ecosystem credentials under Repository Secrets (`Settings > Secrets and Variables > Actions`):
* `AWS_ACCESS_KEY_ID`
* `AWS_SECRET_ACCESS_KEY`
* `AWS_DEFAULT_REGION` (`us-east-1`)
* `ECR_REPO` (Your container registry target URI location)



---

## 🌐 Production Application Verification

1. Push your latest code changes directly upstream to your repository branch to trigger your automated CI/CD pipeline workflow routines.
2. Edit your **AWS Security Group Inbound Rules** configuration to expose traffic ports cleanly:
* **Type:** Custom TCP
* **Port Range:** `5080`
* **Source:** Global (`0.0.0.0/0`)


3. Launch your running production endpoint through any web interface browser:
```text
http://<YOUR_EC2_PUBLIC_IP_ADDRESS>:5080

```


4. **On-Demand Pipelines:** Route web hits directly through the `/training` path directory to trigger active database ingestion routines, pipeline re-runs, and updated metrics evaluation checkpoints.

```

```