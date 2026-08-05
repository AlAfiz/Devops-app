pipeline{
    agent any
    tools {
        nodejs 'my-nodejs'
    }
    stages {
       stage('Installing Dependencies') {
            
           steps {
               script {
                    echo 'Installing Node dependencies....'
                    sh 'npm install'
               }
               
           }
       }
       stage('Package Application') {

           steps {
               script {
               echo 'Packaging the application....'
               sh 'npm pack'
               }
           }
       }
       stage('Build and Push Docker Image') {

           steps {
               script {
               echo 'Building Docker Image and Pushing to Private repo....'
               withCredentials([usernamePassword(credentialsId: 'Docker-Credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                   sh 'docker build -t zacks222/devops-demo-app:npm-1.0 .'
                   sh "echo $PASS | docker login -u $USER --password-stdin"
                   sh 'docker push zacks222/devops-demo-app:npm-1.0'
                    }
                }
            }
        }
       stage("deploy") {
          
           steps {
               echo 'deploying the application....'
           }
       }
    }
}