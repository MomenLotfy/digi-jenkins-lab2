# Jenkins Lab 1 — CI Foundation: Unit Tests, Webhooks & Secrets

## Overview

This lab introduces the fundamentals of Continuous Integration (CI) using Jenkins with a simple PHP web application.

You will configure Jenkins to automatically trigger builds whenever code is pushed to GitHub, execute PHPUnit tests, securely manage secrets, and fail builds when tests do not pass.

---

# Objectives

By completing this lab, you will learn how to:

- Create a Declarative Jenkins Pipeline using a `Jenkinsfile`
- Configure GitHub Webhooks to trigger automatic builds
- Execute automated unit tests using PHPUnit
- Securely store sensitive credentials in Jenkins
- Fail builds automatically when tests fail
- Understand Jenkins pipeline stages and console logs

---

# Technologies Used

- Jenkins
- GitHub
- PHPUnit
- PHP
- Docker
- Ngrok

---

# Lab Scenario

You are provided with a simple PHP web application that contains:

- HTML/CSS/JavaScript frontend
- PHP backend logic

The source code is hosted in a public GitHub repository.

Your task is to configure Jenkins to:

1. Automatically trigger builds on every GitHub push
2. Execute PHPUnit tests
3. Store GitHub Personal Access Token securely in Jenkins
4. Fail the pipeline when tests fail

---

# Prerequisites

Before starting the lab, ensure you have:

- Jenkins installed and running
- Docker installed
- Git installed
- Access to GitHub
- Ngrok installed on the host machine

---

# Step 1 — Clone the Repository

Clone the lab repository:

```bash
git clone https://github.com/ahmedsamyabdullah/digi-jenkins-lab.git
```

---

# Step 2 — Install PHPUnit Inside Jenkins Container

Access the Jenkins container:

```bash
docker exec -u root -it jenkins bash
```

Update packages:

```bash
apt update
```

Install PHP and required extensions:

```bash
apt install -y php-cli php-mbstring php-xml php-curl unzip
```

Install PHPUnit:

```bash
wget -O /usr/local/bin/phpunit https://phar.phpunit.de/phpunit-9.phar
```

Make PHPUnit executable:

```bash
chmod +x /usr/local/bin/phpunit
```

Verify installation:

```bash
phpunit --version
```

---

# Step 3 — Create GitHub Personal Access Token (PAT)

Navigate to GitHub:

```text
Settings → Developer Settings → Personal Access Tokens → Tokens (Classic)
```

Create a new token with the following scopes:

- `repo`
- `admin:repo_hook`

> Important:
> Save your token securely because GitHub will display it only once.

---

# Step 4 — Store GitHub Token in Jenkins Credentials

Inside Jenkins:

```text
Manage Jenkins → Credentials → Global → Add Credentials
```

Configuration:

| Field | Value |
|---|---|
| Kind | Secret Text |
| Secret | Your GitHub PAT |
| ID | github-token |
| Scope | Global |

Save the credential.

---

# Step 5 — Install GitHub Integration Plugin

Inside Jenkins:

```text
Manage Jenkins → Plugins → Available Plugins
```

Search and install:

- GitHub Integration Plugin

Restart Jenkins if required.

---

# Step 6 — Configure GitHub Webhook

Go to your GitHub repository:

```text
Repository → Settings → Webhooks → Add Webhook
```

## Configure Payload URL

Use Ngrok to expose Jenkins publicly.

Open PowerShell on Windows host:

```bash
ngrok http http://192.168.x.x:8080
```

> Replace `192.168.x.x` with the IP address of your Ubuntu VM.

Example webhook URL:

```text
https://your-ngrok-url.ngrok.io/github-webhook/
```

> Important:
> The webhook URL must end with:

```text
/github-webhook/
```

## Content Type

Set:

```text
application/json
```

Save the webhook.

---

# Step 7 — Verify Webhook Delivery

Inside GitHub Webhook settings:

```text
Recent Deliveries
```

Ensure the webhook shows:

```text
200 OK
```

This confirms Jenkins is receiving GitHub events successfully.

---

# Step 8 — Create Jenkins Pipeline Job

Inside Jenkins:

```text
New Item → Pipeline
```

Configure:

| Setting | Value |
|---|---|
| Pipeline Type | Pipeline script from SCM |
| SCM | Git |
| Repository URL | Your GitHub Repository |
| Script Path | Jenkinsfile |

Enable:

```text
GitHub hook trigger for GITScm polling
```

Save the job.

---

# Step 9 — Create the Jenkinsfile

Create a file named:

```text
Jenkinsfile
```

Example pipeline:

```groovy
pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                git 'https://github.com/ahmedsamyabdullah/digi-jenkins-lab.git'
            }
        }

        stage('Run Unit Tests') {
            steps {
                sh 'phpunit tests'
            }
        }

    }

    post {

        success {
            echo 'Build completed successfully.'
        }

        failure {
            echo 'Build failed.'
        }

    }
}
```

---

# Step 10 — Test the CI Pipeline

## Trigger Failed Build

Modify the PHP logic or unit test intentionally to break the test.

Commit and push changes:

```bash
git add .
git commit -m "Break unit test"
git push
```

Observe Jenkins automatically triggering the pipeline.

The build should fail.

---

## Fix the Test

Correct the issue and push again:

```bash
git add .
git commit -m "Fix unit test"
git push
```

Jenkins should trigger a new successful build automatically.

---

# Expected Outcomes

After completing this lab, you should have:

- A working Jenkins Declarative Pipeline
- Automated GitHub-triggered builds
- PHPUnit integrated into CI workflow
- Secure credential management in Jenkins
- Automatic build validation through unit testing

---

# Key Learning Concepts

## Continuous Integration (CI)

CI is the practice of automatically building and testing code whenever developers push changes.

## Jenkins Pipeline

A Jenkins Pipeline defines the CI/CD workflow as code using Groovy syntax.

## Webhooks

GitHub Webhooks allow GitHub to notify Jenkins automatically whenever repository events occur.

## Secrets Management

Sensitive information such as tokens should never be hardcoded inside pipelines.

## Unit Testing

Unit tests validate application logic automatically before deployment.

---

# Troubleshooting

## Webhook Not Triggering

Check:

- Ngrok is running
- Payload URL is correct
- URL ends with `/github-webhook/`
- Jenkins is accessible publicly

---

## PHPUnit Command Not Found

Verify installation:

```bash
phpunit --version
```

Ensure executable permissions are applied.

---

## Build Fails Immediately

Check:

- Repository URL
- Jenkins credentials
- Jenkins console logs
- PHPUnit test paths

---

# Conclusion

This lab demonstrates the foundational concepts of CI using Jenkins, GitHub Webhooks, and PHPUnit.

You now have a complete automated workflow that:

- Detects source code changes
- Executes automated tests
- Validates application integrity
- Provides immediate developer feedback

These concepts form the foundation of modern DevOps and CI/CD practices.
# digi-jenkins-lab2
