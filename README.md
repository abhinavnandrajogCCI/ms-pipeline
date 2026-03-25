# 🚀 MuleSoft CI/CD Pipeline — GitHub Actions

> Automated Build, Publish & Deploy pipeline for MuleSoft applications using GitHub Actions and Anypoint Platform.

---

## 📌 Overview

This project implements a fully automated **Continuous Integration and Continuous Deployment (CI/CD)** pipeline for a MuleSoft application. It transitions from manual deployment methods to a **Push-to-Deploy** architecture, ensuring every code change is automatically validated, and deployed to the Anypoint Platform.

---

## 🎯 Objectives

- Automate the full MuleSoft application lifecycle: **Build → Test → Publish → Deploy**
- Publish artifacts to **Anypoint Exchange** for versioning and reuse
- Deploy to target environments (**Design / Sandbox**) on **CloudHub** via Connected Apps
- Support configurable deployments with control over **vCores** and **replicas**

---

## 🏗️ Architecture

```
Developer Push / Manual Trigger
        │
        ▼
┌─────────────────────────────┐
│      GitHub Actions         │
│                             │
│  1. Setup Java 17 + Maven   │
│  2. Configure Maven Settings│
│  3. Deploy to Exchange      │
│  4. Deploy to Environment   │
└─────────────────────────────┘
        │
        ▼
  Anypoint Platform
  (CloudHub )
```

## ⚙️ Pipeline Inputs

The pipeline is triggered manually via **`workflow_dispatch`** with the following inputs:

| Input | Description | Options |
|---|---|---|
| `repository` | Target repository name | Any string |
| `branch` | Branch to build | `dev`, `main` |
| `environment` | Deployment environment | `Design`, `Sandbox` |
| `vCore` | vCore size on CloudHub | `0.1`, `0.2`, `0.5`, `1`, `2`, `4` |
| `replicas` | Number of replicas | `1` – `8` |

---

## 🔐 Required Secrets & Variables

Configure the following in your GitHub repository under **Settings → Secrets and Variables → Actions**:

### Secrets

| Secret | Description |
|---|---|
| `ANYPOINT_CLIENT_ID` | Anypoint Platform Client ID |
| `ANYPOINT_CLIENT_SECRET` | Anypoint Platform Client Secret |
| `CONNECTED_APP_CLIENT_ID` | Connected App Client ID for deployment |
| `CONNECTED_APP_CLIENT_SECRET` | Connected App Client Secret for deployment |
| `CONNECTED_APP_STRING` | Connected App credentials string for Exchange auth |
| `GRANT_TYPE` | OAuth grant type (e.g., `client_credentials`) |
| `PAT_TOKEN` | Fine-Grained Personal Access Token for repo checkout |

### Variables

| Variable | Description |
|---|---|
| `TARGET` | Deployment target (e.g., CloudHub 2.0 Private Space name) |

---

## 🔑 PAT Token Setup

This pipeline uses a **Fine-Grained Personal Access Token** to check out repositories dynamically.

**To create one:**
1. Go to **GitHub → Settings → Developer Settings → Personal Access Tokens → Fine-grained tokens**
2. Click **Generate new token**
3. Set an expiration date
4. Under **Repository access**, select the specific repositories needed
5. Grant the following permissions:
   - `Contents` → **Read**
   - `Metadata` → **Read**
6. Copy the token and save it as the `PAT_TOKEN` secret in your repository

---

## 📦 Pipeline Steps

### 1. Checkout Repository
Checks out the specified repository and branch using the Fine-Grained PAT token.

### 2. Setup Java 17
Installs **Temurin JDK 17** using `actions/setup-java`.

### 3. Setup Maven 3.9.9
Installs **Maven 3.9.9** using `s4u/setup-maven-action`.

### 4. Configure Maven Settings
Configures Maven with Anypoint Exchange credentials to allow publishing and resolving artifacts from Exchange.

### 5. Deploy to Exchange
Publishes the built artifact to **Anypoint Exchange** using `mvn clean deploy -DskipTests`.

### 6. Deploy to Environment
Deploys the application to the selected environment on the Anypoint Platform using the MuleSoft Maven plugin with all required parameters:
- App name (formatted as `<repository>-<environment>`)
- vCores and replicas
- Connected App credentials
- Anypoint Platform credentials

---

## 🚦 How to Trigger the Pipeline

1. Go to your repository on GitHub
2. Navigate to **Actions → Build-Publish-Deploy**
3. Click **Run workflow**
4. Fill in the required inputs:
   - Repository name
   - Branch
   - Environment
   - vCore size
   - Number of replicas
5. Click **Run workflow** to start the pipeline

---

## 📁 Project Structure

```
├── .github/
│   └── workflows/
│       └── build-publish-deploy.yml   # Main CI/CD pipeline
├── src/
│   ├── main/
│   │   └── mule/                      # MuleSoft application flows
├── pom.xml                            # Maven project configuration
└── README.md
```

---

## 🛠️ Tech Stack

| Tool | Version | Purpose |
|---|---|---|
| GitHub Actions | Latest | CI/CD orchestration |
| Java (Temurin) | 17 | Runtime |
| Maven | 3.9.9 | Build & dependency management |
| MuleSoft | 4.x | Integration platform |
| Anypoint Exchange | v3 | Artifact registry |
| CloudHub | 2.0 | Deployment target |

---

## 📄 License

This project was created as part of a CI/CD implementation assignment for MuleSoft application automation.
