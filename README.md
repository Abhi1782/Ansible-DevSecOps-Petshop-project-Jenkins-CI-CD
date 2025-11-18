# 🛠️ Ansible-DevSecOps-Petshop-project-Jenkins-CI-CD

 We will be deploying a Petshop Java-based application. This is an everyday use case scenario used by several organizations. We will be using Jenkins as a CI/CD tool and deploying our  application on a Docker container

# 🏗 Project Overview

 This project demonstrates a complete DevSecOps CI/CD pipeline for a Java-based Petshop application.
 The pipeline integrates Jenkins, SonarQube, OWASP Dependency Check, Trivy, Ansible, and Docker to automate build, test, analysis, security scanning, and deployment.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1536" height="1024" alt="Project Overview" src="https://github.com/user-attachments/assets/28b3bfd1-5e39-4831-99b6-985fbc518181" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🧩 Tools & Technologies Used

 1) AWS EC2 (Ubuntu 22.04)	Infrastructure host for Jenkins, Docker, SonarQube
 2) Jenkins	CI/CD automation tool
 3) Maven	Build and dependency management
 4) SonarQube	Code quality and static analysis
 5) OWASP Dependency Check	Vulnerability scanning for dependencies
 6) Trivy	Container image vulnerability scanning
 7) Docker	Application containerization and deployment
 8) Ansible	Configuration management and deployment automation
 9) GitHub	Source code repository

# 🚀 CI/CD Pipeline Stages

### 1️⃣ Jenkins Setup

 @ Installed Jenkins on Ubuntu EC2. T2.medium Server and 30 GB EBS.
 And we configured the port number 8090 instead of 8080.

 Installed required plugins:

 1) JDK (Temurin 17)
 2) Maven Integration
 3) SonarQube Scanner
 4) OWASP Dependency Check
 5) Docker Pipeline
 6) Ansible Plugin

### 2️⃣ SonarQube Configuration

 1) Deployed SonarQube via Docker on port 9000
 2) Generated SonarQube authentication token
 3) Configured SonarQube server in Jenkins under “Manage Jenkins → System”
 4) Created Quality Gates & Webhook integration with Jenkins

## Jenkins Pipeline Execution – Stage 1 (Successful Build)

The Jenkins pipeline was successfully created and executed for the Petshop DevSecOps Project.
In this stage, the pipeline performs essential build steps such as workspace cleanup, source code checkout, and Maven compilation.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f259a5bb-9f1b-466c-8ef3-00fea435833b" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🧠 SonarQube Configuration and Integration with Jenkins

After completing the initial Maven build stage, we set up SonarQube to perform code quality analysis and ensure our application meets secure coding standards.

## ⚙️ Step 1: Deploy SonarQube on Docker
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
sudo chmod 777 /var/run/docker.sock

## Run SonarQube container
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

@ Once the container is running, open your browser and access the SonarQube dashboard:
http://<EC2-Public-IP>:9000

## Default Credentials:
Username: admin
Password: admin

<img width="1920" height="1080" alt="Screenshot (385)" src="https://github.com/user-attachments/assets/42ec6a03-5969-4696-80f2-d000249eeea2" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1920" height="1080" alt="Screenshot (387)" src="https://github.com/user-attachments/assets/2bf48205-7f5f-44c4-9e67-29e0d0210fb5" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## ⚙️ Step 2: Generate SonarQube Token

1) Log in to SonarQube → Administration → Security → Users
2) Click on your user and Generate Token (e.g., Sonar-token)
3) Copy this token — it will be used in Jenkins for authentication

<img width="1920" height="1080" alt="Screenshot (395)" src="https://github.com/user-attachments/assets/d0be1d07-39b6-45fe-8089-bbd73426bcf2" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

## ⚙️ Step 3: Configure SonarQube in Jenkins

1) Go to Manage Jenkins → Manage Plugins
   a) Install the SonarQube Scanner plugin

2) Then go to Manage Jenkins → Configure System
   a) Scroll to SonarQube Servers
   b) Click Add SonarQube
   c) Enter:
        Name: sonar-server
        Server URL: http://<EC2-Public-IP>:9000
        Authentication Token: (paste the generated token)
   d) Click Apply & Save

3) Go to Manage Jenkins → Global Tool Configuration
   a) Add SonarQube Scanner tool named sonar-scanner

## ⚙️ Step 4: Add SonarQube Stage to Jenkins Pipeline

        pipeline {
            agent any
            tools {
                jdk 'jdk17'
                maven 'maven3'
            }
            environment {
                SCANNER_HOME = tool 'sonar-scanner'
            }
            stages {
                stage('Checkout SCM') {
                    steps {
                        git 'https://github.com/Aj7Ay/jpetstore-6.git'
                    }
                }
    
            stage('Maven Build') {
                steps {
                    sh 'mvn clean compile'
                }
            }
    
            stage('SonarQube Analysis') {
                steps {
                    withSonarQubeEnv('sonar-server') {
                        sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectName=Petshop \
                        -Dsonar.java.binaries=. \
                        -Dsonar.projectKey=Petshop
                        '''
                    }
                }
            }
    
            stage('Quality Gate') {
                steps {
                    script {
                        waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                    }
                }
            }
        }
    }

---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🧾 Result
Once the build runs successfully, the SonarQube dashboard will show detailed metrics such as:

 Code Smells
 Vulnerabilities
 Duplications
 Test Coverage
 Maintainability & Reliability Ratings

You can view the project report in SonarQube under Projects → Petshop.

http://<EC2-Public-IP>:9000/dashboard?id=Petshop

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1920" height="1080" alt="Screenshot (415)" src="https://github.com/user-attachments/assets/32dba10d-8ced-4e75-b8bf-42f1486c4b72" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1920" height="1080" alt="Screenshot (406)" src="https://github.com/user-attachments/assets/b98ca823-b7ae-481f-bb66-dc5ae0737521" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🧱 Build WAR File and OWASP Dependency Check Integration

After completing the SonarQube static code analysis, the next step in our DevSecOps pipeline focuses on:

1) Building the deployable .war file using Maven, and
2) Integrating OWASP Dependency Check to scan for known vulnerabilities in third-party libraries.

## ⚙️ Step 1: Install OWASP Dependency Check Plugin in Jenkins

1) Navigate to Manage Jenkins → Manage Plugins → Available Plugins
2) Search for and install the plugin:
  a) OWASP Dependency-Check Plugin
3) Once installed, go to Manage Jenkins → Global Tool Configuration
  a) Under Dependency-Check installations, click Add Dependency-Check
  b) Provide a name, e.g., DP-Check
  c) Click Apply & Save

## ⚙️ Step 2: Update Jenkins Pipeline

We now add two new stages to the Jenkins pipeline:

1) Build WAR file – packages the compiled code into a deployable artifact.
2) OWASP Dependency Check – scans for vulnerabilities in dependencies.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

     pipeline {
         agent any
         tools {
             jdk 'jdk17'
             maven 'maven3'
         }
         environment {
             SCANNER_HOME = tool 'sonar-scanner'
         }
         stages {
             stage('Checkout SCM') {
                 steps {
                     git 'https://github.com/Aj7Ay/jpetstore-6.git'
                 }
             }
     
             stage('Maven Compile & Test') {
                 steps {
                     sh 'mvn clean compile'
                     sh 'mvn test'
                 }
             }
     
             stage('SonarQube Analysis') {
                 steps {
                     withSonarQubeEnv('sonar-server') {
                         sh '''
                         $SCANNER_HOME/bin/sonar-scanner \
                         -Dsonar.projectName=Petshop \
                         -Dsonar.java.binaries=. \
                         -Dsonar.projectKey=Petshop
                         '''
                     }
                 }
             }
     
             stage('Quality Gate') {
                 steps {
                     script {
                         waitForQualityGate abortPipeline: false, credentialsId: 'Sonar-token'
                     }
                 }
             }
     
             stage('Build WAR File') {
                 steps {
                     echo 'Building the application WAR file...'
                     sh 'mvn clean install -DskipTests=true'
                 }
             }
     
             stage('OWASP Dependency Check') {
                 steps {
                     echo 'Running OWASP Dependency Check...'
                     dependencyCheck additionalArguments: '--scan ./ --format XML', odcInstallation: 'DP-Check'
                     dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
                 }
             }
         }
     }

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 📊 Step 3: Analyze OWASP Report

Once the Jenkins pipeline runs successfully:
  1) The OWASP plugin automatically generates a vulnerability report in XML format.
  2) You can view the results directly from the Jenkins Project Dashboard under Dependency Check Reports.
  3) The report includes:
     a) Detected CVEs (Common Vulnerabilities and Exposures)
     b) Severity levels (Low, Medium, High)
     c) Affected dependencies and versions

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/4e2a120f-cc8f-49b0-96f2-1b584575a434" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/85bb814e-54b6-42f0-bcd3-e6a9a35dd0e9" />
 
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🐳 Docker Hub Integration and Ansible Configuration

After completing the OWASP Dependency Check stage, the next steps involve integrating Docker and Ansible into the Jenkins pipeline.
This ensures automated Docker image creation, push to Docker Hub, and container deployment using Ansible playbooks.

## ⚙️ Step 1: Install Docker and Required Jenkins Plugins

🧩 Install Docker on Jenkins Server

Run the following commands on your Jenkins EC2 instance:

sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
sudo chmod 777 /var/run/docker.sock

🧩 Verify Installation

docker --version

## ⚙️ Step 2: Configure DockerHub Credentials in Jenkins

1) Go to Manage Jenkins → Manage Credentials → Global → Add Credentials
2) Fill in the following:
   a) Kind: Username and Password
   b) Username: Your DockerHub username
   c) Password: Your DockerHub password or Personal Access Token
   d) ID: dockerhub-credentials (You will use this ID in Jenkins pipeline)
3) Click Create

## ⚙️ Step 3: Generate DockerHub Personal Access Token (PAT)

1) Log in to your DockerHub Account
2) Go to Account Settings → Security → New Access Token
3) Generate a new token and name it (e.g., jenkins-automation)
4) Copy the token and store it securely — this will be used inside the Ansible playbook for DockerHub authentication.

## ⚙️ Step 4: Add Ansible Repository and Install Ansible on Ubuntu

🧩 Add Ansible Repository
sudo apt-get update
sudo apt install software-properties-common -y
sudo add-apt-repository --yes --update ppa:ansible/ansible

🧩 Install Ansible
sudo apt install ansible -y
sudo apt install ansible-core -y

🧩 Verify Installation
ansible --version

## ⚙️ Step 5: Configure Ansible Host Inventory

Edit the Ansible hosts file:
sudo vi /etc/ansible/hosts

Add your Jenkins or target system under a group:
[docker]
<target-machine-public-ip>

Save and exit the file.

## ⚙️ Step 6: Configure Ansible in Jenkins

1) Go to Manage Jenkins → Manage Plugins
     a) Ensure the Ansible Plugin is installed.
2) Go to Manage Jenkins → Global Tool Configuration
     a) Under Ansible installations, click Add Ansible
     b) Name: ansible
     c) Path: (Find it using)
          which ansible
     e) Click Apply & Save
3) Go to Manage Jenkins → Credentials → Add Credentials
   a) Choose Kind: SSH Username with Private Key
   b) Add your PEM key (used for Ansible connection)
   c) ID: ssh
   
----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
  
  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/aafb596b-e154-48ad-ad4b-4aefd36c7b04" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

 <img width="1920" height="1080" alt="Screenshot (430)" src="https://github.com/user-attachments/assets/66d55bde-280f-4b73-97ee-f0f3a9dd47c7" />

 ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# ✅ Outcome

   1) Docker is successfully installed and integrated with Jenkins.
   2) DockerHub credentials and Personal Access Token are securely configured.
   3) Ansible is installed and connected to Jenkins for automated deployment.
   4) The system is now ready to build Docker images, push to DockerHub, and deploy containers using Ansible in the next pipeline stage.

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1278" height="591" alt="Screenshot 2025-10-17 215225" src="https://github.com/user-attachments/assets/0ef63e7d-1a3f-4f31-a45e-eaaa52261974" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# ✅ Project Completion Summary
   
This Petshop DevSecOps Project has been successfully implemented using a complete CI/CD pipeline with security and automation at every stage. The objective of the project was to build, test, scan, package, and deploy the Petshop Java Web Application using modern DevOps tools and best practices.


## 📌 Key Outcomes

✅ Fully automated CI/CD pipeline
✅ DevSecOps practices implemented
✅ Secure, scalable, and containerized deployment
✅ Improved code quality and vulnerability management
✅ No manual intervention required during deployment
✅ Ready for further container orchestration (Kubernetes, ECS, etc.)


## 📂 Project Deliverables

✅ Jenkins Pipeline Script
✅ Dockerfile & DockerHub Push
✅ SonarQube Dashboard Reports
✅ OWASP XML Vulnerability Report
✅ Ansible Playbook for Deployment
✅ Complete GitHub Documentation (this README)


## 🏁 Final Result

The Petshop web application is now:
✔ Successfully built & packaged as a WAR file
✔ Secured using automated DevSecOps checks
✔ Stored in DockerHub as a reusable image
✔ Deployed via Ansible into a running container

⚡️This project demonstrates professional real-world DevSecOps automation with Continuous Integration, Continuous Security & Continuous Deployment.
