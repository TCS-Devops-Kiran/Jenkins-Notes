# Jenkins Administration & Plugins – Advanced Notes

## 1. Enabling and Disabling Projects (Jobs)
In Jenkins, jobs can be temporarily disabled instead of deleting them.

- **Enable a Job:**  
  Dashboard → Select Job → Click **Enable** (if disabled)
- **Disable a Job:**  
  Dashboard → Select Job → Click **Disable Project**  
  > This prevents accidental builds without losing configuration.

---

## 2. Important Jenkins Plugins

### Deploy to Container
- Deploys applications (e.g., WAR files) into application servers like Tomcat.

### Maven Integration
- Allows creation of Maven-based Jenkins jobs.
- Integrates Maven build lifecycle and POM.xml management.

### Safe Restart
- **When we have no Linux server access** but need to restart Jenkins:
  - Restart: `http://<jenkins_ip>:8080/restart`
  - Safe Restart: `http://<jenkins_ip>:8080/safeRestart`

**Difference:**
| Restart | Safe Restart |
|---------|--------------|
| Stops running jobs immediately and restarts Jenkins | Waits for all running jobs to complete before restarting |

> For Safe Restart, install the **safeRestart** plugin.

---

### Next Build Number
- Allows setting the next build number manually.
- The new number must be greater than the last build number.
- **Without server access:** Use plugin instead of editing `/var/lib/jenkins/jobs/<job_name>/nextBuildNumber`.

---

### JaCoCo
- Measures **code coverage**.
- Can stop deployment if coverage is below a configured threshold.

---

### SSH Agent
- Manages SSH keys for connecting to remote servers in pipelines.

---

### Audit Trail
- Tracks Jenkins activities (job creation, deletion, configuration changes).

**Setup:**
1. Install plugin
2. Dashboard → Manage Jenkins → System → Audit Trail  
3. Add Logger → Configure:
   - Log file: `/var/lib/jenkins/audit-trail.log`
   - Size: 20 MB
   - Log file count: 5

**Log Rotation:**
audit-trail.log.0
audit-trail.log.1
audit-trail.log.2
audit-trail.log.3
audit-trail.log.4
audit-trail.log.5

bash
Copy
Edit

Check logs:
```bash
tail -f audit-trail.log.0
Blue Ocean
Modern UI for Jenkins pipelines with visual build stages.

Build Name and Description Setter
Allows setting custom build names/descriptions dynamically.

Example:

Job Configuration → Build Environment → Set Build Name → project-#${BUILD_NUMBER}

Thin Backup
Backs up Jenkins jobs, configs, and plugins.

Will be covered in Jenkins backup notes.

Role-Based Authorization Strategy
Provides fine-grained security with roles and permissions.

Will be covered in Jenkins security notes.

3. Build with Parameters
Steps:

Dashboard → Job → Configure → General → Select This project is parameterized

Add parameter → Choice Parameter

Name: BranchNames

Choices:

css
Copy
Edit
main
dev
qa
Save

Usage in Source Code Management:

bash
Copy
Edit
*/${BranchNames}
4. Creating a View
Dashboard → + (New View) → Select List View or My View

Configure filters (by job name, regex, status, etc.)

Useful for organizing jobs by environment, application, or team.

5. Interview Questions & Answers (5 Years Experience)
Q1. How do you perform a safe restart in Jenkins without Linux access?
A: Use the URL http://<jenkins_ip>:8080/safeRestart (requires Safe Restart plugin). This waits for all running jobs to complete before restarting.

Q2. What’s the difference between “Disable Job” and “Delete Job”?
A: Disable prevents builds but keeps the job configuration. Delete removes the job completely, including configuration and history.

Q3. How do you track configuration changes in Jenkins?
A: Using the Audit Trail plugin, which logs changes to a file for review.

Q4. How can you change the next build number in Jenkins without server access?
A: Install and use the Next Build Number plugin.

Q5. How do you stop deployment if code coverage is low?
A: Use the JaCoCo plugin with a quality gate threshold in the Jenkins job configuration.

Q6. How do you organize jobs for multiple projects in Jenkins?
A: Use Views to group jobs by project, environment, or team.

6. Real-Time Scenario-Based Q&A
Scenario 1: Safe Restart Required During a Release
Situation: A plugin update requires Jenkins restart but multiple jobs are running.
Action: Use safeRestart instead of restart to avoid disrupting running builds.
Risk Avoided: Prevents failed builds during deployment.

Scenario 2: Unauthorized Job Creation Detected
Situation: A job appeared in Jenkins without proper approval.
Action: Check Audit Trail logs to identify the user and time of creation.
Prevention: Implement Role-Based Authorization Strategy.

Scenario 3: Low Code Coverage Failing Builds
Situation: Deployment pipeline stops due to JaCoCo failing the quality gate.
Action: Review JaCoCo report, add missing unit tests, re-run build.
Prevention: Enforce code coverage policy during code review.

Scenario 4: Build Number Needs Reset for Compliance
Situation: Client requires build numbers to start from a specific point.
Action: Use Next Build Number plugin (only increasing numbers allowed).

7. Troubleshooting & Challenges
Challenge 1: Jenkins Restart without Linux Access
Solution: Use restart or safeRestart URL endpoints.

Challenge 2: Missing Audit Logs
Solution: Check Audit Trail plugin configuration, ensure log file path is writable by Jenkins.

Challenge 3: Wrong Branch Built in Parameterized Job
Solution: Verify ${BranchNames} parameter mapping in SCM configuration.

Challenge 4: Jenkins Disk Space Full
Solution:

Discard old builds

Archive only required artifacts

Move Jenkins home to a larger volume
