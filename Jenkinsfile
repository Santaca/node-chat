pipeline {
    agent any
    
    environment {
        registry = '${ECR_URI}'
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/Santaca/node-chat.git']]])   
            }
        }
        
        stage('Docker build') {
            steps {
                script {
                    dockerImage = docker.build registry
                }
            }
        }
        
        stage('Docker Push') {
            steps {
                script {
                    sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${ECR_URI}'
                   sh 'docker push ${ECR_URI}'
                }
            }
        }
        
        stage("Clean docker containers") {
            steps {
                script {
                    sh 'docker rm -f $(docker ps -aq)'
                }
            }
        }
        
        stage("Run Docker Container") {
            steps {
                script {
                    sh 'docker run -d -p 3000:3000 --name unirtest ${ECR_URI}:latest'
                }
            }
        }
    }
}
