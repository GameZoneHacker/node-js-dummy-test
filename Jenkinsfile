pipeline {
    agent any

    environment {
        registry = "sc0rpion/my-node-app"
        registryCredential = 'dockerhub'
        dockerImage = ''
    }

    stages {
        stage('Cloning Git') {
            steps {
                git 'https://github.com/GameZoneHacker/node-js-dummy-test'
            }
        }
        stage('Building Docker Image') {
            steps {
                script {
                    dockerImage = docker.build("${registry}:latest", ".")
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    // Example using Trivy for security scan
                    sh 'docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image ${dockerImage.imageName()}'
                }
            }
        }
        stage('Deploying Image') {
            steps {
                script {
                    docker.withRegistry('', registryCredential) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Clean up') {
            steps {
                script {
                    dockerImage.remove()
                    sh "docker rmi ${registry}:latest"
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
