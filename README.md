# **DevOps CI/CD Pipeline with Monitoring Setup**

This repository provides an implementation of a **CI/CD pipeline** using **Jenkins** for build automation, **SonarQube** for code quality checks, **Nexus** for artifact management, **Docker** for containerization, **Kubernetes** for deployment, and **Prometheus** with **Grafana** for monitoring.

## **Project Overview**

The project follows the stages of a typical DevOps pipeline, including:

1. **Git Repository Setup**: Code is hosted in a private Git repository (GitHub).
2. **CI/CD Pipeline**: Jenkins automates the entire process, including:
   - Cloning the repository
   - Code compilation and testing using Maven
   - Quality checks with SonarQube
   - Docker image creation and scanning
   - Pushing the image to Docker Hub
   - Deployment to Kubernetes
3. **Monitoring**: Prometheus and Grafana are used for monitoring and visualizing metrics from the infrastructure and deployed services.

## **Tools and Technologies**

- **Jenkins**: CI/CD automation.
- **SonarQube**: Code quality and security analysis.
- **Maven**: Build tool for Java projects.
- **Docker**: Containerization of the application.
- **Kubernetes**: Orchestration and deployment of containers.
- **Prometheus**: Monitoring and alerting toolkit.
- **Grafana**: Visualization of monitoring metrics.

## **Project Setup**

### **1. Jenkins CI/CD Setup**

To set up Jenkins with necessary plugins for this pipeline, follow the steps below:

1. **Install Plugins in Jenkins**:
    - **Eclipse Temurin Installer**
    - **Pipeline Maven Integration**
    - **Config File Provider**
    - **SonarQube Scanner**
    - **Kubernetes CLI**
    - **Kubernetes**
    - **Docker**
    - **Docker Pipeline Step**

   To install these plugins, navigate to the Jenkins Dashboard → **Manage Jenkins** → **Manage Plugins** → **Available** tab. Search and install the necessary plugins.

2. **Pipeline Configuration**:
    - The pipeline is defined in `Jenkinsfile` within the repository. It includes stages for:
      - Git checkout
      - Compilation and testing
      - Quality analysis with SonarQube
      - Docker image build, scan, and push
      - Deployment to Kubernetes
      - Monitoring integration

   Example of the pipeline stages:
   ```groovy
   pipeline {
       agent any
       stages {
           stage('Git Checkout') { steps { git 'https://github.com/Sammy0254/DevOps-Pipeline.git' } }
           stage('Compile') { steps { sh "mvn compile" } }
           stage('SonarQube Analysis') { steps { sh "sonar-scanner" } }
           stage('Build & Push Docker Image') { steps { sh "docker build -t my-app . && docker push my-app" } }
           stage('Deploy to Kubernetes') { steps { sh "kubectl apply -f deployment.yaml" } }
       }
   }

   To Monitor, use Jenkins
