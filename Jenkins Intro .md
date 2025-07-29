

## Flow Diagram: CI/CD Pipeline Overview

```text
developer
    ↓
GitHub (Source Code Management)
    ↓
Maven (Build Tool)
    ↓
SonarQube (Code Quality Analysis)
    ↓
Nexus (Artifact Repository)
    ↓
Tomcat (Deployment Server)
    ↓
Mail (Notification to Stakeholders)
```

This flow represents a standard CI/CD pipeline in Jenkins:

* **Developer** commits code to GitHub.
* Jenkins polls GitHub or uses a webhook to trigger a job.
* **Maven** compiles and packages the code.
* **SonarQube** scans for code quality and vulnerabilities.
* **Nexus** stores the build artifact (e.g., `.jar` or `.war`).
* Jenkins deploys the artifact to **Tomcat** server.
* After successful deployment, Jenkins sends a notification email.

---

## What is Jenkins?

Jenkins is an open-source **Continuous Integration (CI)** tool written in Java. It is widely used for automating tasks related to building, testing, and deploying code.

* Created by **Kohsuke Kawaguchi** in 2004.
* Initially called **Hudson**, renamed to **Jenkins** in 2011.

### Advantages of Jenkins

* Free and open source with strong community support.
* Easy to install and configure using a web-based GUI.
* Has **1900+ plugins** to extend its functionality.
* Platform-independent (built in Java).
* Rich documentation and tutorials available.

---

## Continuous Integration (CI)

CI is the practice of merging all developers' working copies to a shared repository several times a day. Jenkins helps in automating this process.

### CI Flow Diagram

```text
Developer → GitHub → Jenkins → Build + Unit Test → Code Analysis (SonarQube)
```

### CI Benefits

* Immediate bug detection
* Fewer merge conflicts
* Frequent deploys
* Improved code quality and collaboration
* Immediate feedback loop for developers

---

## Continuous Delivery (CD)

CD ensures that every successful build that passes all tests can be deployed to production manually with minimal effort.

### CD Flow Diagram

```text
Code → Unit Tests → Integrate → Acceptance Tests → Manual Deployment
        AUTO         AUTO         AUTO                MANUAL
```

**Note:** Deployment requires approval from client or QA.

---

## Continuous Deployment

This practice goes one step further by automating deployment to production without human intervention.

### Continuous Deployment Flow

```text
Code → Unit Tests → Integrate → Acceptance Tests → Auto Deployment
        AUTO         AUTO         AUTO                AUTO
```

---

## Real-Time Scenario: CI/CD in Projects

**Q: Which one do you use – Continuous Delivery or Continuous Deployment?**

**A:**

* For **in-house/internal projects** (e.g., HR systems), we use **Continuous Deployment**.
* For **client projects** (e.g., Jio, Flipkart), we follow **Continuous Delivery** to ensure manual control before production releases.

---

## What Jenkins Can Do

* Integrates with Version Control Systems (GitHub, GitLab, BitBucket)
* Generates test reports (JUnit, TestNG)
* Publishes build artifacts to Nexus or JFrog Artifactory
* Deploys to production/test environments
* Sends notifications via Email, Slack, etc.

---

## Popular CI Tools

| S.No | Tool Name         | Open Source |
| ---- | ----------------- | ----------- |
| 1    | Jenkins           | Yes         |
| 2    | Cloudbees Jenkins | No          |
| 3    | Bamboo            | No          |
| 4    | Cruise Control    | Yes         |
| 5    | Travis CI         | Yes / Paid  |
| 6    | Circle CI         | Yes / Paid  |
| 7    | GitLab CI         | Yes / Paid  |
| 8    | TeamCity          | Yes / Paid  |

**Note:** Jenkins default port is **8080**.

---

## Jenkins Installation on CentOS/RHEL

### Prerequisites

* Java (recommended: OpenJDK 21)
* EC2 instance (min: t2.medium)

### Java Installation

```bash
sudo yum install java-21-openjdk -y
```

### Jenkins Installation Steps

```bash
# Switch to root
sudo su -

# Install wget and utilities
yum install wget tree -y

# Add Jenkins repository
wget -O /etc/yum.repos.d/jenkins.repo \
    https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

# Upgrade packages
yum upgrade -y

# Install Jenkins and dependencies
yum install fontconfig java-21-openjdk jenkins -y

# Reload systemd and enable Jenkins
systemctl daemon-reload
systemctl enable jenkins
systemctl start jenkins

# Check status
systemctl status jenkins
```

### Access Jenkins

* Open browser: `http://<public-ip>:8080`
* Allow port 8080 in AWS security group

### Unlock Jenkins

```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```

### Setup Wizard

* Choose "Install Suggested Plugins"
* Create admin user:

  * Username: kkfunda
  * Password: kkfunda
  * Full Name: KK FUNDA
* Finish setup and start using Jenkins

---

## Common Challenges and Troubleshooting

### 1. Jenkins Port Not Accessible

**Cause:** Port 8080 not allowed in EC2 security group
**Solution:** Add an inbound rule to allow TCP port 8080

### 2. Jenkins Service Fails to Start

**Check:**

```bash
journalctl -xe
systemctl status jenkins
```

**Fix:** Ensure Java is installed and configured properly

### 3. Plugin Installation Fails

**Solution:**

* Check internet access from the server
* Restart Jenkins and retry plugin installation

### 4. High CPU Usage

**Cause:** Too many parallel jobs or heavy builds
**Solution:**

* Configure job throttling
* Upgrade instance size

---

## Real-Time Use Cases (5+ Years Experience)

### Scenario 1: Code Quality Enforcement with SonarQube

**Problem:** Developers were merging low-quality code
**Solution:** Integrated SonarQube in Jenkins. Build fails if code quality score is low.

### Scenario 2: Multiple Environment Deployments

**Need:** Deploy to DEV, QA, STAGE, PROD
**Solution:** Used parameterized builds and `choice` parameter in Jenkins pipelines

### Scenario 3: Notification on Build Failures

**Need:** Inform developers immediately on failure
**Solution:** Configured email plugin and integrated with Slack for instant alerts

### Scenario 4: Scheduled Builds for Nightly Test Automation

**Action:** Used Jenkins' cron feature to run nightly regression jobs

---

## Conclusion

Jenkins is a powerful automation tool widely used for CI/CD pipelines. With proper plugin integration and job configurations, Jenkins can handle all stages of your software delivery lifecycle. Understanding real-time scenarios, troubleshooting issues, and optimizing performance are crucial for effective Jenkins usage in production environments.


