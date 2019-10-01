pipeline {   
  agent {
    node {
      label 'master'
    }  
  }
  stages {
/*     stage('delete files from workspace') {
      steps {
        sh 'ls -l'
        sh 'sudo rm -rf ./*'
      }
    } */
    stage('checkout') {
      steps {
        sh 'docker pull cytopia/terragrunt:latest'
      }
    }
    /* stage('init') {
      steps {
        sh 'docker run -w /app/non-prod/us-east-1 -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY -v `pwd`:/app cytopia/terragrunt:latest terragrunt init-all'
      }
    } */
    stage('dependencies') {
      steps {
        sh 'docker run cytopia/terragrunt:latest apk add --update openssh-client'
      }
    }   
    stage('plan') {
      steps {
                withCredentials([[
                $class: 'AmazonWebServicesCredentialsBinding',
                credentialsId: 'tfdev1-user',
                accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                secretKeyVariable: 'AWS_SECRET_ACCESS_KEY']]) {
        sh 'docker run -w /app/non-prod/us-east-1 -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY -e TF_VAR_master_password="password" -v `pwd`:/app cytopia/terragrunt:latest terragrunt plan-all'
      }
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
        sh 'docker run -w /app/non-prod/us-east-1 -e AWS_ACCESS_KEY_ID=$AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY=$AWS_SECRET_ACCESS_KEY -e TF_VAR_master_password=$TF_VAR_master_password -v `pwd`:/app cytopia/terragrunt:latest terragrunt apply-all -auto-approve'
        cleanWs()
      }
    }
  }
}