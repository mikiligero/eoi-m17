pipeline {
    agent any
    environment {
        registry = "mikiligero78/m17-e2"
        registryCredentials = "dockerhub"
        repositoryCredentials = "github"
        project = "m17-e2"
        projectVersion = "1.0"
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
        stage('Code Analysis'){
            environment{
                scannerHome= tool 'Sonar'
            }
            steps{
                script{
                    withSonarQubeEnv('Sonar'){
                        sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=$project \
                        -Dsonar.projectName=$project \
                        -Dsonar.projectVersion=$projectVersion \
                        -Dsonar.sources=./"
                    }
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
