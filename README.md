# 🚀 DummyJSON API Testing - Jenkins Workflow

This directory contains the necessary configuration for running an automated CI/CD API testing pipeline for the `dummyjson-api-testing` project using **Jenkins** ⚙️, **Newman** 🏃 (Postman's command-line collection runner), and **htmlextra** 📊 for reporting.

## 🌟 Overview

The `Jenkinsfile` defines a declarative Jenkins pipeline configured to:
1. 📥 Pull the testing codebase from the repository.
2. 🛠️ Setup the necessary reporting and testing tools globally.
3. 🧪 Execute the postman collection using Newman.
4. 📈 Generate a visually informative HTML report.
5. 📂 Archive the test results and send email notifications 📧 based on the outcome of the job.

## 🔄 Workflow Stages breakdown

### 1️⃣ Environment & Setup
- **Agent** 🤖: The pipeline can run on `any` available Jenkins agent.
- **Tools Requirement** 📦: Requires `nodejs 'Node18'` to be configured in Jenkins to run Newman via npm.
- **Credentials Manager** 🔐: Exposes a configured Jenkins credential named `GITHUB_TOKEN` to be safely used within the testing environment without being hardcoded.

### 2️⃣ Stages
- **Checkout Code** 💻: 
  Clones the `main` branch from the GitHub repository (`https://github.com/Dasun169/dummyjson-api-testing.git`).
  
- **Install Dependencies** 🔌: 
  Uses npm to install the required tools globally on the Jenkins agent:
  - `newman`: The command-line engine to run Postman collections.
  - `newman-reporter-htmlextra`: A custom reporter that generates advanced HTML reports.

- **Run API Tests** 🔥: 
  Executes the testing script via a Windows batch command (`bat`):
    - 📁 Uses the `Newman_Test.json` collection file.
    - 🌐 Applies `dummyJSON.postman_environment.json` for environment configurations.
    - 🔑 Dynamically overrides the `access_token` variable with the securely stored `%GITHUB_TOKEN%`.
    - 📤 Configures Newman to output both to the command `cli` and via `htmlextra`.
    - 🎯 The HTML report is actively exported into a local generated `reports/api-test-report.html` file.

### 3️⃣ Post-execution Actions
After the stages run, regardless of success, Jenkins will execute these steps:

- **Always** 📌:
  - 🗄️ Archives all artifacts from the `reports/` directory so they are preserved and viewable within the Jenkins job dashboard over time.
  
- **Success** ✅:
  - 📩 Sends a "Passed" notification email to `www.dasun544@gmail.com`.
  - 📊 The email explicitly states qualitative metrics natively (e.g., verifying 17 API Endpoints and 87 Validations).
  - 📎 Actively attaches the generated HTML report file to the email.
  
- **Failure** ❌:
  - 🚨 Sends a "Failed" notification email to the same address.
  - 🔍 Recommends inspecting the actively attached HTML report to discover the root cause of the failure.

## 📂 Files Structure Context

- 📜 `Jenkinsfile`: The core declarative pipeline file orchestrating the steps above.
- 🧪 `Newman_Test.json`: The exported Postman Collection populated with requests and assertions.
- ⚙️ `dummyJSON.postman_environment.json`: The Postman Environment variables configuration file defining initial environment states (like URLs or dummy tokens).

## ⚠️ Prerequisites

To correctly run this testing workflow on Jenkins, your Jenkins server needs to have:
1. 🟢 The **NodeJS plugin** properly installed and configured with a node instance named `Node18`.
2. 🔑 A stored **Secret Text** credential in Jenkins with the ID `GITHUB_TOKEN`.
3. 📧 The **Email Extension Plugin** properly configured with an SMTP server to allow sending post-build emails.
