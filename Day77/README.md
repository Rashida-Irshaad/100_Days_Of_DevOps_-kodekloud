# Day 77 - Challenge
 # KodeKloud Jenkins Pipeline Task — Complete Solution

## Task Description

The development team of xFusionCorp Industries wants to deploy a static website using a Jenkins Pipeline.

Requirements:

- Jenkins login:
  - Username: `admin`
  - Password: `Adm!n321`
- Gitea login:
  - Username: `sarah`
  - Password: `Sarah_pass123`
- Repository: `sarah/web_app`
- Repository is already cloned on App Server 1 at:
  `/var/www/html`
- App Server 1 hostname:
  `stapp01`
- Apache is already installed and running on port `8080`.
- Create a Jenkins agent named:
  `App Server 1`
- Agent label:
  `stapp01`
- Agent remote root:
  `/home/sarah/jenkins_agent`
- Create a Jenkins Pipeline job named:
  `datacenter-webapp-job`
- It must be a normal **Pipeline**, NOT a Multibranch Pipeline.
- The pipeline must contain exactly one stage named:
  `Deploy`
- Deploy the website directly into:
  `/var/www/html`
- The final website must load from the main Load Balancer URL, not `/web_app`.

---

## 1. Verify App Server 1

From the Jumphost:

    ssh sarah@stapp01

Password:

    Sarah_pass123

Verify:

    whoami
    hostname
    ls -la /var/www/html

Expected:

    sarah
    stapp01

The repository should contain `.git` and `index.html`.

Verify the Git repository:

    cd /var/www/html
    git remote -v
    git branch --show-current

Expected repository:

    http://sarah:Sarah_pass123@gitea:3000/sarah/web_app.git

Expected branch:

    master

---

## 2. Install the Jenkins SSH Agent Plugin

In Jenkins:

    Manage Jenkins
    → Plugins
    → Available plugins

Search for:

    SSH Build Agents

Install it.

Restart Jenkins if requested.

---

## 3. Create the Jenkins Credential

Go to:

    Manage Jenkins
    → Credentials
    → System
    → Global credentials
    → Add Credentials

Select:

    Kind: Username with password

Enter:

    Username: sarah
    Password: Sarah_pass123
    Description: stapp01-sarah

Leave the ID blank.

Click **Create**.

Important: use **Username with password**, NOT SSH Username with private key.

---

## 4. Create the Jenkins Agent

Go to:

    Manage Jenkins
    → Nodes
    → New Node

Node name:

    App Server 1

Select:

    Permanent Agent

Click **Create**.

Configure:

    Number of executors: 1

    Remote root directory:
    /home/sarah/jenkins_agent

    Labels:
    stapp01

For Launch method select:

    Launch agents via SSH

Enter:

    Host:
    stapp01

    Credentials:
    sarah / stapp01-sarah

    Port:
    22

For Host Key Verification Strategy select:

    Non verifying Verification Strategy

Save.

---

## 5. Fix Java Version if the Agent Fails

If the node log shows:

    UnsupportedClassVersionError

and says:

    class file version 61.0
    recognizes up to 55.0

then App Server 1 has Java 11 while Jenkins requires Java 17.

On `stapp01`, run:

    sudo yum install -y java-17-openjdk java-17-openjdk-devel

Then:

    sudo alternatives --config java

Select Java 17.

Verify:

    java -version

The agent should then connect successfully.

Expected final node status:

    App Server 1 — Online

---

## 6. Install Jenkins Pipeline Plugin

Go to:

    Jenkins
    → Manage Jenkins
    → Plugins
    → Available plugins

Search for:

    Pipeline

Install the Pipeline plugin/bundle offered by Jenkins.

If Jenkins asks for a restart, select:

    Restart Jenkins when installation is complete and no jobs are running

Refresh the Jenkins page after restart.

---

## 7. Create the Pipeline Job

Go to:

    Jenkins Dashboard
    → New Item

Job name:

    datacenter-webapp-job

Select:

    Pipeline

Do NOT select:

    Multibranch Pipeline

Click **OK**.

---

## 8. Configure the Pipeline

Scroll down to the **Pipeline** section.

Set:

    Definition:
    Pipeline script

Paste this script:

    pipeline {
        agent {
            label 'stapp01'
        }

        stages {
            stage('Deploy') {
                steps {
                    sh '''
                        cd /var/www/html
                        git pull origin master
                    '''
                }
            }
        }
    }

Click **Save**.

---

## 9. Run the Pipeline

Click:

    Build Now

Then open:

    Build History
    → Build #1
    → Console Output

A successful build should end with:

    Finished: SUCCESS

If the output says:

    Already up to date.

that is also fine. It means the App Server already has the latest repository contents.

---

## 10. Verify the Website

Click the **App** button in the KodeKloud lab.

The website must load directly at:

    https://<LBR-URL>

It must NOT require:

    https://<LBR-URL>/web_app

The deployment location must remain:

    /var/www/html

---

## Final Configuration

Jenkins Node:

    Name: App Server 1
    Host: stapp01
    Label: stapp01
    Remote root: /home/sarah/jenkins_agent
    SSH user: sarah
    Status: Online

Jenkins Job:

    Name: datacenter-webapp-job
    Type: Pipeline
    Multibranch Pipeline: No

Pipeline:

    Agent: stapp01
    Stage: Deploy
    Deployment directory: /var/www/html

Pipeline script:

    pipeline {
        agent {
            label 'stapp01'
        }

        stages {
            stage('Deploy') {
                steps {
                    sh '''
                        cd /var/www/html
                        git pull origin master
                    '''
                }
            }
        }
    }

## Important Lessons from the Setup

- Use `sarah`, not `tony`, for the Jenkins agent because `/home/sarah/jenkins_agent` and `/var/www/html` belong to `sarah`.
- Use **Username with password** for the Jenkins SSH credential.
- The SSH password is `Sarah_pass123`.
- The Jenkins agent requires Java 17.
- The node label must be exactly `stapp01`.
- The Pipeline stage must be exactly `Deploy` (case-sensitive).
- The job must be a normal **Pipeline**, not a Multibranch Pipeline.
- Deploy directly to `/var/www/html`; do not create `/var/www/html/web_app`.
