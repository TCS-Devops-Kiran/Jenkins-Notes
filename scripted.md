# Jenkins Scripted Pipeline – Maven → SonarQube → Nexus → Tomcat

## Overview
This pipeline performs:
1. **Checkout** source code from GitHub  
2. **Build** and package application using Maven  
3. **Run SonarQube code analysis**  
4. **Upload artifacts to Nexus Repository**  
5. **Deploy WAR to Tomcat server** via `curl`

---

## Scripted Pipeline Code

```groovy
node {
    // Define Maven Tool
    def mavenHome = tool name: "maven-3.9.8"

    stage('Checkout') {
        git branch: 'development',
            credentialsId: 'e5b73679-3105-4429-a5b4-b303fd9e35a0',
            url: 'https://github.com/kkeducation12345/maven-web-app-project-kk-funda.git'
    }

    stage('Build') {
        sh "${mavenHome}/bin/mvn clean package"
    }

    stage('SonarQube Analysis') {
        withSonarQubeEnv('SonarQubeServer') {
            sh "${mavenHome}/bin/mvn sonar:sonar"
        }
    }

    stage('Upload to Nexus') {
        sh "${mavenHome}/bin/mvn deploy"
    }

    stage('Deploy to Tomcat') {
        echo "Deploying WAR file to Tomcat..."
        sh """
            curl -u kkfunda:password \
            --upload-file /var/lib/jenkins/workspace/${JOB_NAME}/target/maven-web-application.war \
            "http://3.108.194.157:8080/manager/text/deploy?path=/maven-web-application&update=true"
        """
    }
}
Key Improvements Made
Used withSonarQubeEnv() for SonarQube token & URL management instead of hardcoding.

Removed redundant Maven executions (no need to run clean package in every stage).

Used ${JOB_NAME} to avoid hardcoding workspace path.

Credentials & URLs externalized (use Jenkins credentials & global variables).

Stages logically separated to improve debugging.

Jenkins Interview Questions & Answers
Q1: What’s the difference between Freestyle, Maven, and Pipeline jobs?
A:

Freestyle: Simple configuration, limited customization.

Maven: Integrates Maven lifecycle but limited control over execution order.

Pipeline: Uses Groovy, supports complex CI/CD flows, full control.

Q2: Why choose Scripted Pipeline over Declarative Pipeline?
A:

Scripted gives more flexibility and control (ideal for complex conditions/loops).

Declarative is easier to read and maintain for simple pipelines.

Q3: How do you integrate SonarQube with Jenkins?
A:

Install SonarQube Scanner plugin.

Configure SonarQube server under Manage Jenkins → Configure System.

Use withSonarQubeEnv('serverName') in pipeline.

Q4: How do you deploy artifacts to Tomcat from Jenkins?
A:

Enable Tomcat Manager App and create a deploy user in tomcat-users.xml.

Use curl with basic auth to upload WAR file via Tomcat’s /manager/text/deploy API.

Q5: How do you secure credentials in Jenkins pipelines?
A:

Use Jenkins credentials store (Secret Text, Username/Password, etc.).

Access them with withCredentials() or credentials IDs.

Real-Time Scenarios
Multi-Environment Deployments

Deploy to Dev, QA, Prod using the same pipeline but different parameters.

Quality Gate Enforcement

Stop pipeline if SonarQube Quality Gate fails.

Parallel Stages

Run Unit Tests and Static Code Analysis in parallel to save build time.

Challenges & Solutions
Challenge	Cause	Solution
SonarQube analysis fails	Missing scanner or wrong project key	Verify SonarQube plugin & project settings
Nexus upload fails	Wrong credentials or URL	Configure Nexus credentials in Jenkins & verify repo URL
Tomcat deploy fails with 403	Missing role in Tomcat	Add <role rolename="manager-script"/> & assign to user
Build fails on clean workspace	Dependencies missing	Use Maven repository cache or build once & reuse artifacts
Pipeline fails in middle	One stage fails	Use try-catch or post blocks for cleanup/notifications

Troubleshooting Steps
SonarQube Issues

Check Jenkins console logs for sonar.projectKey errors.

Verify withSonarQubeEnv() configuration.

Test connection in Jenkins global settings.

Nexus Upload Issues

Validate distributionManagement section in pom.xml.

Run mvn deploy locally to test credentials.

Tomcat Deploy Issues

Check Tomcat logs: $CATALINA_HOME/logs/catalina.out.

Verify user has manager-script role in tomcat-users.xml.

Ensure WAR path is correct in curl command.

Maven Build Fails

Run mvn clean package locally to identify build errors.

Check for missing pom.xml dependencies.

Best Practices
Use Jenkins Shared Libraries for reusable pipeline code.

Store secrets in Jenkins credentials, never hardcode passwords.

Use stage names that clearly describe the action.

Implement post-build notifications (Slack, Email).

Enable Blue Ocean or Stage View for better visualization.
