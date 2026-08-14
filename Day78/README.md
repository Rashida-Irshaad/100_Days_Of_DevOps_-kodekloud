# Day 78 - Challenge 
# KodeKloud Jenkins Pipeline Task — Complete Solution

## Task Description

The xFusionCorp Industries development team needs a Jenkins Pipeline to deploy a static website from the existing Gitea repository.

### Requirements

- Jenkins:
  - Username: `admin`
  - Password: `Adm!n321`
- Gitea:
  - Username: `sarah`
  - Password: `Sarah_pass123`
- Repository: `sarah/web_app`
- Repository already cloned on App Server 1:
  `/var/www/html`
- App Server:
  `stapp01`
- Apache is running on port `8080`.
- Jenkins agent:
  - Name: `App Server 1`
  - Label: `stapp01`
  - Remote root: `/home/sarah/jenkins_agent`
- Jenkins job:
  - Name: `nautilus-webapp-job`
  - Type: Pipeline
  - NOT Multibranch Pipeline
- Add a string parameter named:
  `BRANCH`
- If `BRANCH=master`, deploy the `master` branch.
- If `BRANCH=feature`, deploy the `feature` branch.
- The pipeline must contain exactly one stage:
  `Deploy`
- Deployment directory:
  `/var/www/html`
- Website must load directly from the LB URL and not from `/web_app`.

---

## 1. Verify App Server

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

Check the repository:

    cd /var/www/html
    git remote -v
    git branch --show-current

The repository should be:

    http://sarah:Sarah_pass123@gitea:3000/sarah/web_app.git

---

## 2. Create Jenkins SSH Credential

Go to:

    Jenkins
    → Manage Jenkins
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

Leave ID blank.

Click **Create**.

Do NOT select:

    SSH Username with private key

---

## 3. Create Jenkins Agent

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

    Number of executors:
    1

    Remote root directory:
    /home/sarah/jenkins_agent

    Labels:
    stapp01

Launch method:

    Launch agents via SSH

Host:

    stapp01

Credentials:

    sarah / stapp01-sarah

Port:

    22

Host Key Verification Strategy:

    Non verifying Verification Strategy

Click **Save**.

The node should become:

    App Server 1 — Online

---

## 4. Fix Java if Agent Does Not Start

If the node log shows:

    UnsupportedClassVersionError

with:

    class file version 61.0
    recognizes up to 55.0

then Java 17 is required.

On `stapp01`:

    sudo yum install -y java-17-openjdk java-17-openjdk-devel

Then:

    sudo alternatives --config java

Select Java 17.

Verify:

    java -version

After Java 17 is selected, the Jenkins agent should connect successfully.

---

## 5. Install Pipeline Plugin

Go to:

    Manage Jenkins
    → Plugins
    → Available plugins

Search:

    Pipeline

Install the Pipeline plugin/bundle.

If Jenkins asks for a restart, select:

    Restart Jenkins when installation is complete and no jobs are running

Refresh Jenkins after the restart.

---

## 6. Create the Pipeline Job

Go to:

    Jenkins Dashboard
    → New Item

Enter:

    nautilus-webapp-job

Select:

    Pipeline

Do NOT select:

    Multibranch Pipeline

Click **OK**.

---

## 7. Add the BRANCH Parameter

In the job configuration, enable:

    This project is parameterized

Click:

    Add Parameter
    → String Parameter

Enter:

    Name:
    BRANCH

    Default Value:
    master

    Description:
    Enter master or feature

---

## 8. Configure the Pipeline

Go to the **Pipeline** section.

Set:

    Definition:
    Pipeline script

Paste:

    pipeline {
        agent {
            label 'stapp01'
        }

        stages {
            stage('Deploy') {
                steps {
                    sh '''
                        cd /var/www/html

                        if [ "$BRANCH" = "master" ]; then
                            git checkout master
                            git pull origin master

                        elif [ "$BRANCH" = "feature" ]; then
                            git checkout feature
                            git pull origin feature

                        else
                            echo "Invalid branch: $BRANCH"
                            exit 1
                        fi
                    '''
                }
            }
        }
    }

Click **Save**.

---

## 9. Test Master Branch

Click:

    Build with Parameters

Set:

    BRANCH = master

Click **Build**.

The pipeline should execute:

    git checkout master
    git pull origin master

The build should finish with:

    Finished: SUCCESS

---

## 10. Test Feature Branch

Again click:

    Build with Parameters

Set:

    BRANCH = feature

Click **Build**.

The pipeline should execute:

    git checkout feature
    git pull origin feature

The build should finish with:

    Finished: SUCCESS

---

## 11. Verify Deployment

The files must be deployed directly into:

    /var/www/html

Do NOT deploy into:

    /var/www/html/web_app

Click the **App** button in the KodeKloud lab.

The website should load directly at:

    https://<LBR-URL>

It should NOT require:

    https://<LBR-URL>/web_app

---

# Final Configuration

## Jenkins Agent

    Name: App Server 1
    Host: stapp01
    Label: stapp01
    Remote root: /home/sarah/jenkins_agent
    SSH user: sarah
    Status: Online

## Jenkins Job

    Name: nautilus-webapp-job
    Type: Pipeline
    Multibranch Pipeline: No

## Parameter

    Name: BRANCH
    Type: String
    Default: master

## Pipeline

    Agent: stapp01
    Stage: Deploy
    Deployment directory: /var/www/html

## Pipeline Script

    pipeline {
        agent {
            label 'stapp01'
        }

        stages {
            stage('Deploy') {
                steps {
                    sh '''
                        cd /var/www/html

                        if [ "$BRANCH" = "master" ]; then
                            git checkout master
                            git pull origin master

                        elif [ "$BRANCH" = "feature" ]; then
                            git checkout feature
                            git pull origin feature

                        else
                            echo "Invalid branch: $BRANCH"
                            exit 1
                        fi
                    '''
                }
            }
        }
    }

## Important Points

- Use `sarah` for the Jenkins SSH connection.
- Use password `Sarah_pass123`.
- The Jenkins agent must be **Online** before running the job.
- Use Java 17 on the App Server if Jenkins reports a Java version error.
- The job must be a normal **Pipeline**, not Multibranch Pipeline.
- The parameter name must be exactly `BRANCH`.
- The only stage must be exactly `Deploy`.
- `master` deploys the `master` branch.
- `feature` deploys the `feature` branch.
- Deployment must be directly under `/var/www/html`.
- Do not create or use `/var/www/html/web_app`.
- Test both `master` and `feature` using **Build with Parameters**.
