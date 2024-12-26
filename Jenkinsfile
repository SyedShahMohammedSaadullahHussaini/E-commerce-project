pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs() // Clean workspace before starting the build
            }
        }
        
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
                    def gitCommitHash = sh(script: 'git rev-parse --short HEAD', returnStdout: true).trim()
                    bat "docker build -t syedssaad/devops-integration:${gitCommitHash} ."
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

        stage('Deploy') {
            steps {
                script {
                    kubernetesDeploy(configs: 'deploymentservice.yaml', kubeconfigId: 'k8configpwd')
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment successful!'
            // Add success notification (e.g., email, Slack)
        }
        failure {
            echo 'Deployment failed!'
            // Add failure notification (e.g., email, Slack)
        }
    }
}
