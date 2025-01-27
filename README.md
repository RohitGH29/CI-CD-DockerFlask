# Flask Application Deployment Using Jenkins CI/CD Pipeline and Docker

This project demonstrates how to set up a CI/CD pipeline using Jenkins to deploy a Flask application on Docker. The pipeline includes stages such as code checkout, installing dependencies, building a Docker image, pushing it to Docker Hub, and deploying it on Docker. This project is designed to help others understand the deployment process step by step.

---

## Prerequisites

Before starting, ensure you have the following:

1. **EC2 Instance**:
   - Launch an Ubuntu EC2 instance.
   - Open ports `8080` for Jenkins and `5000` for the Flask application.




---

## Step 1: Install Tools on Your EC2 Instance

1. **Docker Installed**:
   - Install Docker and configure it:
     ```bash
     sudo apt update
     sudo apt install docker.io -y
     sudo usermod -aG docker ubuntu && newgrp docker
     ```
2. **Install Python:**
     ```bash
     sudo apt install python3-venv -y
     ```


## Step 2: Install Jenkins on Your EC2 Instance

1. **Install**:
   ```bash
    sudo apt update -y
    sudo apt install fontconfig openjdk-17-jre -y

    sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
    https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
  
    echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
    https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
  
    sudo apt-get update -y
    sudo apt-get install jenkins -y
   ```
 *Access Jenkins: Open http://<EC2_PUBLIC_IP>:8080 in your browser.*

2. **Use the initial admin password from:**
    ```bash
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
    
3. **Add Jenkins User to Docker Group:**
    ```bash
    sudo usermod -aG docker jenkins
    ```

## Step 3 : Configure Jenkins

1. **Install Plugins:**
    -Docker
    -Pipeline: Stage View

2. **Create Credentials:**
Add Docker Hub credentials (username and token/password) under Manage Jenkins > Credentials.

3. **Create a New Pipeline Job in Jenkins:**
    Go to Jenkins Dashboard > New Item > Pipeline > OK.

 *Add the Pipeline Script: Paste the following script into the pipeline definition:*
 ```bash
 pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'your-dockerhub-username/flask-app'
    }
    stages {
        stage('Git: Code Checkout') {
            steps {
                script{
                    code_checkout("https://github.com/RohitGH29/CI-CD-DockerFlask.git","main")
                }
            }
        }
        stage('Install Dependencies') {
            steps {
                echo 'Installing dependencies...'
                sh '''
                    python3 -m venv venv
                    . venv/bin/activate
                    pip install -r requirements.txt
                '''
            }
        }
        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                sh 'docker build -t ${DOCKER_IMAGE} .'
            }
        }
        stage('Push to Docker Hub') {
            steps {
                echo 'Pushing Docker image to Docker Hub...'
                withCredentials([usernamePassword(credentialsId: 'docker-hub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                }
                sh 'docker push ${DOCKER_IMAGE}'
            }
        }
        stage('Deploy on Docker') {
            steps {
                echo 'Deploying application...'
                sh '''
                docker stop flask-app || true
                docker rm flask-app || true
                docker run -d -p 5000:5000 --name flask-app ${DOCKER_IMAGE}
                '''
            }
        }
    }
}
```

## Step 4: Run the Pipeline
1. Trigger the pipeline from Jenkins.
2. The pipeline will:
    Checkout the Flask application code.
    Install dependencies from requirements.txt.
    Build a Docker image.
    Push the image to Docker Hub.
    Deploy the Flask application as a Docker container.

## Step 5: Verify Deployment
1. Open your browser and visit: http://<EC2_PUBLIC_IP>:5000.
2. You should see the message: "Welcome! You successfully deployed the Flask application on Docker using Jenkins pipeline.
"    