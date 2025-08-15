You can save it as cron_jenkins_notes.md and open it in any Markdown viewer or GitHub for nice formatting.

# Cron, Jenkins Scheduling & Real-Time Scenarios

## 1. Crontab – Scheduling Tasks

Crontab is used to schedule scripts or commands to run automatically at defined times and intervals.

### Syntax


/path/to/command

| | | | |
| | | | +---- Day of the week (0 - 6) (Sunday = 0)
| | | +------ Month (1 - 12)
| | +-------- Day of the month (1 - 31)
| +---------- Hour (0 - 23)
+------------ Minute (0 - 59)


### Key Commands
| Command | Description |
|---------|-------------|
| `crontab -l` | List current user’s cron jobs |
| `crontab -e` | Edit cron jobs |
| `crontab -r` | Remove all cron jobs |
| `crontab -l -u <user>` | View another user’s cron jobs (root only) |

### Access Control
- `/etc/cron.allow` → Only users listed here can use cron  
- `/etc/cron.deny` → Users listed here cannot use cron  

### Example Script
```bash
#!/bin/bash
echo "Welcome to KK FUNDA"
echo "Today's date is:"
date
uptime
pwd


Run Script:

sh hello.sh
# OR (make executable first)
chmod u+x hello.sh
./hello.sh


Schedule Script Every Minute & Append Logs:

*/1 * * * * /home/ec2-user/hello.sh >> /home/ec2-user/hello.log

2. Jenkins Build Triggers
SCM Polling

Jenkins checks the source code repository at intervals.

If new commits are found, a build is triggered.

Pros: Always up-to-date with latest changes
Cons: Frequent polling uses server resources

Periodic Builds

Builds happen at a set schedule (cron-like), regardless of code changes.

Pros: Regular builds
Cons: May build without any new changes

GitHub Webhook

GitHub sends a notification to Jenkins when code changes occur.

Setup Steps:

In Jenkins: Enable “GitHub hook trigger for GITScm polling”

In GitHub repo → Settings → Webhooks → Add webhook
Payload URL: http://<jenkins_ip>:8080/github-webhook/
Content type: application/json

3. Code Coverage with JaCoCo

Target: Maintain ≥80% code coverage

Process:

Generate JaCoCo reports in Jenkins

Identify uncovered code

Write additional tests

Configure Jenkins to fail build if coverage < 80%

4. Discard Old Builds in Jenkins

Manage disk space by configuring:

“Discard Old Builds” option in job settings

Example: Keep builds for only 10 days or last 10 builds

5. Jenkins Server IP Update Issue

If Jenkins server IP changes:

Update /var/lib/jenkins/jenkins.model.JenkinsLocationConfiguration.xml

Restart Jenkins:

sudo systemctl restart jenkins

6. Interview Questions & Answers

Q1. What is the difference between SCM polling and GitHub webhook in Jenkins?
A:

SCM Polling: Jenkins checks the repo periodically → delayed builds, more load.

Webhook: GitHub notifies Jenkins instantly → faster, efficient.

Q2. How do you schedule jobs in Jenkins?
A:

Using “Build periodically” with cron syntax (H 2 * * * for daily at 2 AM)

Using SCM Polling (H/15 * * * * every 15 minutes)

Using external triggers like GitHub webhooks.

Q3. How do you handle old build cleanup in Jenkins?
A:

Use “Discard Old Builds” to automatically delete older build artifacts/logs.

Q4. How do you troubleshoot a cron job not running?
A:

Check if cron service is running (systemctl status cron / crond)

Check script permissions (chmod +x script.sh)

Check log file /var/log/cron or /var/log/syslog

Ensure correct absolute paths in cron command

Q5. How do you ensure high code quality in Jenkins builds?
A:

Integrate code analysis tools (SonarQube, Checkstyle, PMD)

Enforce coverage thresholds with JaCoCo

Fail build on quality gate failure

7. Real-Time Scenarios, Challenges & Troubleshooting
Scenario 1: Cron job not executing

Issue: Cron job runs manually but not via crontab
Root Causes:

Relative paths used in script → use absolute paths

Missing environment variables in cron

No execute permissions
Fix:

Use /bin/bash in script’s shebang

Add PATH in cron job

Set execute permissions: chmod +x script.sh

Scenario 2: Jenkins build not triggering via webhook

Possible Causes:

Webhook URL incorrect

Jenkins firewall blocks GitHub request

GitHub secret mismatch
Troubleshooting:

Verify webhook delivery logs in GitHub settings

Ensure Jenkins is accessible publicly or via VPN

Check Jenkins logs for incoming webhook request

Scenario 3: Jenkins disk space full due to old builds

Solution:

Enable “Discard Old Builds”

Archive only necessary artifacts

Use logrotate for Jenkins logs

Move Jenkins workspace to a larger disk

Scenario 4: Jenkins server IP change

Impact:

Webhooks fail (wrong IP)

Email notifications contain old IP
Resolution:

Update Jenkins location config XML

Update webhook URLs in GitHub/GitLab

Restart Jenkins


