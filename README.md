# Ansible-DevSecOps-Petshop-project-Jenkins-CI-CD
We will be deploying a Petshop Java-based application. This is an everyday use case scenario used by several organizations. We will be using Jenkins as a CI/CD tool and deploying our application on a Docker container

# Project Overview

This project demonstrates a complete DevSecOps CI/CD pipeline for a Java-based Petshop application.
The pipeline integrates Jenkins, SonarQube, OWASP Dependency Check, Trivy, Ansible, and Docker to automate build, test, analysis, security scanning, and deployment.

# Tools & Technologies Used
# Tool / Technology	Purpose

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
JDK (Temurin 17)
Maven Integration
SonarQube Scanner
OWASP Dependency Check
Docker Pipeline
Ansible Plugin
