pipeline {
    agent any
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("devocker123/hello-world-app")
                }
            }
        }
        stage('Run Docker Container') {
            steps {
                script {
                    docker.run("devocker123/hello-world-app")
                }
            }
        }
    }
}
