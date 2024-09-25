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
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Security Scan') {
            steps {
                script {
                    // Ensure bash is used for substitution
                    sh '''
                        #!/bin/bash
                        docker run --rm -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy:latest image ${registry}
                    '''
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
                sh "docker rmi ${registry}"
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}
