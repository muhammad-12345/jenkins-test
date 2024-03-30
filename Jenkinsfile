pipeline {
    agent { label 'linux' }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    environment {
        // It's assumed that 'docker' credentials are properly set in Jenkins credentials store
        DOCKERHUB_CREDENTIALS = credentials('docker')
        IMAGE_NAME = 'devocker123/hello-world-app' // Docker image name
    }

    stages {
        stage('Preparation') {
            steps {
                script {
                    echo "Attempting to print Docker and system info..."
                    sh 'docker version || echo "Failed to execute docker version command"'
                    sh 'docker info || echo "Failed to execute docker info command"'
                    echo "Building Docker image: ${env.IMAGE_NAME}"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo "Building the Docker image..."
                    sh "docker build -t ${env.IMAGE_NAME} . || echo 'Failed to build Docker image'"
                }
            }
        }
        stage('Docker Login') {
            steps {
                script {
                    echo "Logging into Docker Hub..."
                    sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin || echo 'Failed to login to Docker Hub'"
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    echo "Running the Docker container..."
                    sh "docker run --name testContainer ${env.IMAGE_NAME} || echo 'Failed to run Docker container'"
                    // Add a cleanup step immediately after run attempt
                    sh "docker rm -f testContainer || true"
                }
            }
        }
        // Since clean up is now done immediately after run attempt, the 'Post-Run Cleanup' stage may be redundant
    }
    post {
        always {
            echo "Logging out of Docker..."
            sh 'docker logout || echo "Failed to logout of Docker"'
            echo 'Build process completed.'
        }
        failure {
            echo 'Build failed. Investigating issues.'
            // Here you can add steps to notify you, like sending an email or a message to a chat service.
        }
    }
}
