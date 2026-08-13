pipeline{
    agent any
    tools {
        nodejs 'my-nodejs'
    }
    stages {
        stage('Clean Workspace') {

           steps {
               script {
                echo 'Removing the old artifact from workspace....'
                sh 'rm -f *.tgz'
               }
           }
       }
       stage('Increment version') {
            
           steps {
               script {
                    echo 'Increment version......'
                    def newVersion = sh(script: "npm version patch --no-git-tag-version", returnStdout: true).trim().replace('v', '')
                    env.IMAGE_NAME = "${newVersion}"
               }
               
           }
       }
       stage('Clean-up and Installing the new Application version') {

           steps {
               script {
                echo 'Installing the application....'
                sh 'npm ci'
               }
           }
       }


       stage('Package the new Application version') {

           steps {
               script {
                echo 'Packaging the new Application version....'
                sh 'npm pack'
               }
           }
       }


       stage('Build and Push Docker Image') {

           steps {
               script {
               echo 'Building Docker Image and Pushing to Private repo....'
               withCredentials([usernamePassword(credentialsId: 'Docker-Credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                   sh "docker build -t zacks222/devops-demo-app:${IMAGE_NAME} ."
                   sh 'echo $PASS | docker login -u $USER --password-stdin'
                   sh "docker push zacks222/devops-demo-app:${IMAGE_NAME}"
                    }
                }
            }
        }

       stage('Updating to Github'){
           steps{
               script{
                echo 'Updating the Github repo with the latest version....'
                withCredentials([usernamePassword(credentialsId: 'github.credentials', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                    sh 'git config user.email "jenkins@example.com"'
                    sh 'git config user.name "jenkins"' 

                    sh "git remote set-url origin https://${USER}:${PASS}@github.com/AlAfiz/Devops-app.git"
                    sh 'git add .'
                    sh 'git commit -m "feat: update the github repo with the latest version"'
                    sh 'git push origin main'
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