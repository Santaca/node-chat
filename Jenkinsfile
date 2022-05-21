pipeline {
    agent any
    environment {
        registry = "244889495851.dkr.ecr.us-east-1.amazonaws.com/unir-jenkins"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/Santaca/node-chat.git']]])     
            }
        }
  
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry
        }
      }
    }
   
    // Uploading Docker images into AWS ECR
    stage('Pushing to ECR') {
     steps{  
         script {
                sh 'aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 244889495851.dkr.ecr.us-east-1.amazonaws.com'
                sh 'docker push 244889495851.dkr.ecr.us-east-1.amazonaws.com/unir-jenkins'
         }
        }
      }
   
         // Stopping Docker containers for cleaner Docker run
     stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=nodechatapp -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=nodechatapp -q | xargs -r docker container rm'
         }
       }
      
    stage('Docker Run') {
     steps{
         script {
                sh 'docker run -d -p 3000:3000 --rm --name nodechatapp 244889495851.dkr.ecr.us-east-1.amazonaws.com/unir-jenkins:latest'
            }
      }
    }
    }
}
