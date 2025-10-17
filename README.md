# Ansible-DevSecOps-Petshop-project-Jenkins-CI-CD
We will be deploying a Petshop Java-based application. This is an everyday use case scenario used by several organizations. We will be using Jenkins as a CI/CD tool and deploying our application on a Docker container

# Project Overview

This project demonstrates a complete DevSecOps CI/CD pipeline for a Java-based Petshop application.
The pipeline integrates Jenkins, SonarQube, OWASP Dependency Check, Trivy, Ansible, and Docker to automate build, test, analysis, security scanning, and deployment.

# Tools & Technologies Used

1) AWS EC2 (Ubuntu 22.04)	Infrastructure host for Jenkins, Docker, SonarQube
2) Jenkins	CI/CD automation tool
3) Maven	Build and dependency management
4) SonarQube	Code quality and static analysis
5) OWASP Dependency Check	Vulnerability scanning for dependencies
6) Trivy	Container image vulnerability scanning
7) Docker	Application containerization and deployment
8) Ansible	Configuration management and deployment automation
9) GitHub	Source code repository

# CI/CD Pipeline Stages
1️⃣ Jenkins Setup

 @ Installed Jenkins on Ubuntu EC2. T2.medium Server and 30 GB EBS.
 And we configured the port number 8090 instead of 8080.

 Installed required plugins:
1) JDK (Temurin 17)
2) Maven Integration
3) SonarQube Scanner
4) OWASP Dependency Check
5) Docker Pipeline
6) Ansible Plugin

2️⃣ SonarQube Configuration

1) Deployed SonarQube via Docker on port 9000
2) Generated SonarQube authentication token
3) Configured SonarQube server in Jenkins under “Manage Jenkins → System”
4) Created Quality Gates & Webhook integration with Jenkins

# Jenkins Pipeline Execution – Stage 1 (Successful Build)

The Jenkins pipeline was successfully created and executed for the Petshop DevSecOps Project.
In this stage, the pipeline performs essential build steps such as workspace cleanup, source code checkout, and Maven compilation.

<img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/f259a5bb-9f1b-466c-8ef3-00fea435833b" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# 🧠 SonarQube Configuration and Integration with Jenkins

After completing the initial Maven build stage, we set up SonarQube to perform code quality analysis and ensure our application meets secure coding standards.

# ⚙️ Step 1: Deploy SonarQube on Docker
sudo apt-get update
sudo apt-get install docker.io -y
sudo usermod -aG docker $USER
newgrp docker
sudo chmod 777 /var/run/docker.sock

# Run SonarQube container
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

@ Once the container is running, open your browser and access the SonarQube dashboard:
http://<EC2-Public-IP>:9000

# Default Credentials:
Username: admin
Password: admin

<img width="1920" height="1080" alt="Screenshot (385)" src="https://github.com/user-attachments/assets/42ec6a03-5969-4696-80f2-d000249eeea2" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

<img width="1920" height="1080" alt="Screenshot (387)" src="https://github.com/user-attachments/assets/2bf48205-7f5f-44c4-9e67-29e0d0210fb5" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# ⚙️ Step 2: Generate SonarQube Token

1) Log in to SonarQube → Administration → Security → Users
2) Click on your user and Generate Token (e.g., Sonar-token)
3) Copy this token — it will be used in Jenkins for authentication

<img width="1920" height="1080" alt="Screenshot (395)" src="https://github.com/user-attachments/assets/d0be1d07-39b6-45fe-8089-bbd73426bcf2" />

-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

# ⚙️ Step 3: Configure SonarQube in Jenkins

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

# ⚙️ Step 4: Add SonarQube Stage to Jenkins Pipeline

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

# 🧾 Result
Once the build runs successfully, the SonarQube dashboard will show detailed metrics such as:

 Code Smells
 Vulnerabilities
 Duplications
 Test Coverage
 Maintainability & Reliability Ratings

You can view the project report in SonarQube under Projects → Petshop.

http://<EC2-Public-IP>:9000/dashboard?id=Petshop

<img width="1920" height="1080" alt="Screenshot (415)" src="https://github.com/user-attachments/assets/32dba10d-8ced-4e75-b8bf-42f1486c4b72" />

----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------


