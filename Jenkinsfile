pipeline {   
  agent {
    node {
      label 'master'
    }  
  }
  stages {
    stage('checkout') {
      steps {
        checkout scm
        sh 'docker pull cytopia/terragrunt:latest'
      }
    }
    stage('init') {
      steps {
        sh 'docker run -w /app/non-prod -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY -v `pwd`:/app cytopia/terragrunt:latest terragrunt init-all'
      }
    }
    stage('plan') {
      steps {
        sh 'docker run -w /app/non-prod -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY -v `pwd`:/app cytopia/terragrunt:latest terragrunt plan-all'
      }
    }
    stage('approval') {
      options {
        timeout(time: 1, unit: 'HOURS') 
      }
      steps {
        input 'approve the plan to proceed and apply'
      }
    }
    stage('apply') {
      steps {
        sh 'docker run -w /app/non-prod -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY -v `pwd`:/app cytopia/terragrunt:latest terragrunt apply-all -auto-approve'
        cleanWs()
      }
    }
  }
}