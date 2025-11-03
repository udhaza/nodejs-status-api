pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "nodejs-status-api"
        DOCKER_TAG = "latest"
        CONTAINER_NAME = "nodejs-api-container"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/<your-username>/nodejs-status-api.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                bat 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %DOCKER_IMAGE%:%DOCKER_TAG% ."
            }
        }

        stage('Run Docker Container') {
            steps {
                // Stop existing container if running
                bat "docker rm -f %CONTAINER_NAME% || exit 0"
                // Run container
                bat "docker run -d -p 3000:3000 --name %CONTAINER_NAME% %DOCKER_IMAGE%:%DOCKER_TAG%"
                // Wait for server to start
                bat "timeout /t 5"
            }
        }

        stage('Test API') {
            steps {
                // Test /status endpoint
                bat "curl http://localhost:3000/status"
            }
        }

        stage('Cleanup') {
            steps {
                // Stop and remove container, remove image
                bat "docker stop %CONTAINER_NAME% || exit 0"
                bat "docker rm %CONTAINER_NAME% || exit 0"
                bat "docker rmi %DOCKER_IMAGE%:%DOCKER_TAG% || exit 0"
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}
