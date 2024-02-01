pipeline {
  environment {
    dockerimagename = "tagost/order"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git branch: 'main', url: 'https://github.com/tagost/order.git'
        sh 'echo $GIT_COMMIT'
      }
    }
    
    stage('Back-end') {
      agent {
        docker { 
            image 'maven:3.8.1-adoptopenjdk-11'
            args '-v ${PWD}:/home/jenkins -w /home/jenkins'
            reuseNode true
        }
      }
      steps {
        sh 'mvn clean package'
        sh 'pwd'
      }
    }
    
    stage('Build docker image') {
      steps{
        sh "pwd"          
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
               registryCredential = 'dockerhub-credentials'
           }
      steps{
        script {
            docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("$BUILD_NUMBER")
            sh 'echo ${GIT_COMMIT}'
          }
        }
      }
    }
    
    stage('Clone Gitops Repo dev Branch') {
      steps {
        git branch: 'main', url: 'https://github.com/tagost/app1-argocd.git'
        sh 'git status'
      }
    }
  }  
}  
