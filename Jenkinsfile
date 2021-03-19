def project = 'affable-anagram-300714' //change
//def  appName = 'gceme'
//def  feSvcName = "${appName}-frontend"
//def  imageTag = "gcr.io/${project}/${appName}:${env.BRANCH_NAME}.${env.BUILD_NUMBER}"

 

pipeline {
  agent {
    kubernetes {
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
  
  - name: kubectl
    image: gcr.io/cloud-builders/kubectl
    command:
    - cat
    tty: true
  volumes: 
     - name: docker-sock 
       hostPath: 
           path: /var/run 
    
"""
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

   

    stage('TF Apply') {
      steps {
        container('terraform') {
          sh 'terraform apply -input=false myplan'
        }
      }
    }

  } 

}
