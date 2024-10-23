pipeline {

agent any
 environment {
     PROJECT_ID = 'jenkins'
     CLUSTER_NAME = 'cluster'
     LOCATION = 'usa'
     CREDENTIALS_ID = 'vultr'
 }
 stages {
     stage("Checkout code") {
         steps {
             checkout scm
         }
     }
     stage("Build image") {
         steps {
             script {
                 myapp = docker.build("ewr.vultrcr.com/cr001/node-app:${env.BUILD_ID}")
             }
         }
     }
     stage("Push image") {
         steps {
             script {
                 docker.withRegistry('https://ewr.vultrcr.com/cr001', 'vcr-credentials') {
                         myapp.push("latest")
                         myapp.push("${env.BUILD_ID}")
                 }
             }
         }
     }

 stage("Deploy to Vultr Kubernetes") {

     steps {
         sh "sed -i 's/node-app:latest/node-app:${env.BUILD_ID}/g' deployment.yml"
                 script {
     withKubeConfig([credentialsId: 'kubeconfig']) 
         {
       sh "kubectl apply -f deployment.yml"
       sh "kubectl apply -f service.yml"

       }                
     }

   }
 }

 }
 }