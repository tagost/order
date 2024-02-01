pipeline {
  environment {
    dockerimagename = "tagost/order"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        //git branch: 'main', url: 'https://github.com/tagost/order.git'
        sh 'true'
      }
    }
    
    stage('Back-end') {
      agent {
        docker { image 'maven:3.8.1-adoptopenjdk-11' }
      }
      steps {
        sh 'mvn clean package'
      }
    }
    
    stage('Build docker image') {
      steps{
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
            dockerImage.push('latest')
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
