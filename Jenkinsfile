
pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'abhijith006/smart-campus-energy'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                bat 'dir'
            }
        }

        stage('Build and Test') {
            parallel {
                stage('Validate HTML') {
                    steps {
                        bat 'findstr /C:"Campus Energy" index.html'
                    }
                }

                stage('Validate Dockerfile') {
                    steps {
                        bat 'findstr /C:"FROM nginx:alpine" Dockerfile'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% .'
                bat 'docker tag %DOCKER_IMAGE%:%BUILD_NUMBER% %DOCKER_IMAGE%:latest'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([string(
                    credentialsId: 'dockerhub-token',
                    variable: 'DOCKERHUB_TOKEN'
                )]) {
                    bat '''
                        @echo off
                        docker login -u abhijith006 -p %DOCKERHUB_TOKEN%
                        docker push %DOCKER_IMAGE%:%BUILD_NUMBER%
                        docker push %DOCKER_IMAGE%:latest
                        docker logout
                    '''
                }
            }
        }

        stage('Archive Files') {
            steps {
                archiveArtifacts artifacts: 'index.html,Dockerfile,deployment.yaml',
                                 fingerprint: true
            }
        }
    }
}
