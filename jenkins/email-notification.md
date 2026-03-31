# Jenkins Email Notification Setup (Pipeline + Gmail)

## Overview

This guide demonstrates how to configure **email notifications in Jenkins pipelines** using:

* Email Extension Plugin
* Pipeline

It assumes Jenkins is already installed and running.

---

## Required Plugins

Ensure the following plugins are installed:

* Email Extension Plugin (`email-ext`)
* Pipeline (pipeline plugin)

Go to: **Manage Jenkins → Plugins → Installed Plugins**



## Configure Gmail SMTP

Go to:
**Manage Jenkins → Configure System**

Scroll to:
**Extended E-mail Notification**

### Configuration

```text
SMTP Server: smtp.gmail.com
SMTP Port: 587

Use SMTP Authentication: YES
Use TLS: YES
Use SSL: NO

Username: your-email@gmail.com
Password: <App Password>
```


## Generate Gmail App Password

Gmail does **NOT allow normal passwords** for Jenkins. You must use an **App Password**.

### Steps:

1. Go to Google Account Security
2. Enable **2-Step Verification (2FA)**
3. Open **App Passwords**
4. Select:

   * App: Mail
   * Device: Other (enter: Jenkins)
5. Click **Generate**

You will get a 16-character password like:

```text
abcd efgh ijkl mnop
```

Use it in Jenkins **without spaces**:

```text
abcdefghijklmnop
```

---

## Test Email Configuration

In the same configuration page:

* Enter recipient email
* Click **“Test configuration by sending test e-mail”**

Expected:

* Email sent successfully
* Received in inbox (or spam)

## Create Pipeline Job

1. Go to Jenkins Dashboard
2. Click **New Item**
3. Select **Pipeline**
4. Choose **Pipeline script (inline)**


## Sample Jenkinsfile

```groovy
pipeline {
    agent any

    environment {
        EMAIL_TO = 'your-email@gmail.com'
    }

    stages {
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }

    post {
        success {
            emailext (
                subject: "SUCCESS: ${env.JOB_NAME}",
                body: "Build successful: ${env.BUILD_URL}",
                to: "${EMAIL_TO}"
            )
        }
        failure {
            emailext (
                subject: "FAILED: ${env.JOB_NAME}",
                body: "Build failed: ${env.BUILD_URL}",
                to: "${EMAIL_TO}"
            )
        }
    }
}
```

## Output

* Email sent on success
* Email sent on failure
