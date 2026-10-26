pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "my-python-app-image"
        APP_PORT = "5000"
    }
    stages {
        stage('Build Python App') {
            steps {
                echo 'Skipping heavy build for simple python script...'
            }
        }
        
        stage('SonarQube Quality Scan') {
            steps {
                echo 'Scanning Code with SonarQube...'
                // A lightweight containerized sonar-scanner
                // Make sure to replace YOUR_TOKEN_HERE with your actual SonarQube token!
                sh '''
                docker run --rm -e SONAR_HOST_URL="http://172.17.0.1:9000" \
                -v "${WORKSPACE}:/usr/src" sonarsource/sonar-scanner-cli \
                -Dsonar.projectKey=my-python-app \
                -Dsonar.sources=. \
                -Dsonar.token=squ_053199b8865382baedaa0c47e30a52296cf3772d
                '''
            }
        }
        
        stage('Docker Build') {
            steps {
                echo 'Building Docker Image...'
                sh "docker build -t ${DOCKER_IMAGE}:latest ."
            }
        }
        
        stage('Trivy Security Scan') {
            steps {
                echo 'Scanning Docker Image for Vulnerabilities...'
                // Scans the image. For beginners, we won't fail the build here, just print results.
                sh "trivy image --no-progress ${DOCKER_IMAGE}:latest"
            }
        }
        
        stage('Deploy Application') {
            steps {
                echo 'Deploying application as a Docker container...'
                sh "docker stop python-app-container || true"
                sh "docker rm python-app-container || true"
                sh "docker run -d -p ${APP_PORT}:${APP_PORT} --name python-app-container ${DOCKER_IMAGE}:latest"
            }
        }
    }
}
