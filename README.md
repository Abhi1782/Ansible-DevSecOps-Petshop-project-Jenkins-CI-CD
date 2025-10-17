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

# Installed Jenkins on Ubuntu EC2

# Installed required plugins:
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
