pipeline {
    agent { label 'linux' }
    options {
        buildDiscarder(logRotator(numToKeepStr: '5'))
    }

    environment {
        // Define environment variables that can be used throughout the pipeline
        DOCKERHUB_CREDENTIALS = credentials('docker') // Adjust this to your configured credentials ID
    }

    stages {
        stage('Preparation') {
            steps {
                script {
                    // Print Docker and system info to help diagnose environment-related issues
                    sh 'docker version'
                    sh 'docker info'
                    echo "Building Docker image: devocker123/hello-world-app"
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    try {
                        // Attempt to build the Docker image
                        sh "docker build -t devocker123/hello-world-app ."
                    } catch (Exception err) {
                        // If the build fails, catch the exception and print an error message
                        echo "Error building Docker image: ${err.getMessage()}"
                        // Fail the build so it's clear an error has occurred
                        error "Stopping build due to build error"
                    }
                }
            }
        }
        stage('login') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    try {
                        // Attempt to run the Docker container
                        sh "docker run --name testContainer devocker123/hello-world-app"
                    } catch (Exception err) {
                        // If running the container fails, catch the exception and print an error message
                        echo "Error running Docker container: ${err.getMessage()}"
                        // Optionally clean up if needed
                        sh "docker rm testContainer -f"
                        // Fail the build so it's clear an error has occurred
                        error "Stopping build due to container run error"
                    } finally {
                        // Always try to remove the container even if the run fails
                        sh "docker rm -f testContainer || true"
                    }
                }
            }
        }
        stage('Post-Run Cleanup') {
            steps {
                script {
                    // Clean up the test container after run, regardless of success/failure
                    sh "docker rm -f testContainer || true"
                    echo "Cleanup completed."
                }
            }
        }
    }
    post {
        always {
            sh 'docker logout'
            // This block ensures that some actions are taken after the pipeline runs, regardless of the result.
            echo 'Build completed.'
        }
        failure {
            // Actions to take if the pipeline fails. For example, notify someone or log the failure.
            echo 'Build failed. Investigating issues.'
        }
    }
}
