pipeline {

  agent {
    kubernetes{
    label 'sample-app'
defaultContainer 'jnlp'
yaml """
apiVersion: v1
kind: Pod
metadata:
labels:
component: ci
spec:
# Use service account that can deploy to all namespaces
serviceAccountName: cd-jenkins
containers:
- name: terraform
image: hashicorp/terraform
command:
- cat
 tty: true
    }
  }
  

  environment {
    SVC_ACCOUNT_KEY = credentials('terraform-auth')
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
        sh 'mkdir -p creds' 
        sh 'echo $SVC_ACCOUNT_KEY > ./creds/serviceaccount.json'
      }
    }

    stage('TF Plan') {
      steps {
        container('terraform') {
          sh 'terraform init'
          sh 'terraform plan -out myplan'
        }
      }      
    }

    stage('Approval') {
      steps {
        script {
          def userInput = input(id: 'confirm', message: 'Apply Terraform?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Apply terraform', name: 'confirm'] ])
        }
      }
    }

    stage('TF Apply') {
      steps {
        container('terraform') {
          sh 'terraform apply -input=false myplan'
        }
      }
    }

  } 

}
