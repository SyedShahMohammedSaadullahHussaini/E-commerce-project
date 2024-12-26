pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage('Build Maven') {
            steps {
                checkout scmGit(
                    branches: [[name: '*/main']],
                    extensions: [],
                    userRemoteConfigs: [[url: 'https://github.com/SyedShahMohammedSaadullahHussaini/E-commerce-project.git']]
                )
                bat 'mvn clean install'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    bat 'docker build -t syedssaad/devops-integration .'
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-access', variable: 'DOCKERHUB_PWD')]) {
                    script {
                        bat """
                        echo %DOCKERHUB_PWD% | docker login -u syedssaad --password-stdin
                        """
                    }
                }
                bat 'docker push syedssaad/devops-integration'
            }
        }
    }
}
