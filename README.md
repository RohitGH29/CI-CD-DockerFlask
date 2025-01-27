# Flask Application Deployment Using Jenkins CI/CD Pipeline and Docker

This project demonstrates how to set up a CI/CD pipeline using Jenkins to deploy a Flask application on Docker. The pipeline includes stages such as code checkout, installing dependencies, building a Docker image, pushing it to Docker Hub, and deploying it on Docker. This project is designed to help others understand the deployment process step by step.

---

## Prerequisites

Before starting, ensure you have the following:

1. **EC2 Instance**:
   - Launch an Ubuntu EC2 instance.
   - Open ports `8080` for Jenkins and `5000` for the Flask application.




---

## Step 1: Install Docker on Your EC2 Instance

1. **Docker Installed**:
   - Install Docker and configure it:
     ```bash
     sudo apt update
     sudo apt install docker.io -y
     sudo usermod -aG docker ubuntu && newgrp docker
     ```

## Step 2: Install Jenkins on Your EC2 Instance

1. **Install Java**:
   ```bash
   sudo apt install openjdk-11-jdk -y
   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
   /usr/share/keyrings/jenkins-keyring.asc > /dev/null
   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
   https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
   /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt update
   sudo apt install jenkins -y
   ```
