pipeline {
    agent any
    environment {
        registry = "mikiligero78/m17-e2"
        registryCredentials = "dockerhub"
        repositoryCredentials = "github"
        project = "m17-e2"
        repository = "https://github.com/mikiligero/eoi-m17.git"
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout code') {
            steps {
                script {
                    git branch: 'main',
                        credentialsId: repositoryCredentials,
                        url: repository
                }
            }
        }
        stage('Build') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    try {
                        sh 'docker run --name $project $registry'
                    } finally {
                        sh 'docker rm $project'
                    }
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    docker.withRegistry('', registryCredentials) {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Cleaning Up') {
            steps {
                script {
                    sh 'docker rmi $registry'
                }
            }
        }
    }
    post {
        success {
            echo 'El pipeline ha finalizado con éxito.'
        } 
        failure {
            echo 'El pipeline ha fallado.'
        }
    }
}
