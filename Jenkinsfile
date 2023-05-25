pipeline {

    agent {
        label 'my-ssh-agent-2'
    }

    tools {
        maven 'maven'
        //nodejs 'nodejs'
        dockerTool 'docker'
    }
    
    triggers {
        githubPush()
        
    }
   environment {
        DOCKERHUB_CREDENTIALS = credentials('my_dockerhub_credentials')
        IMAGE_NAME = 'georgikrastev/mynodejsapp'
    }

    stages {

    //    stage('Clean') {
    //        steps {
    //            cleanWs()
    //       }
    //    }

        stage('Clone repo') {
            steps {
                git branch: 'main', url: 'https://github.com/georgi-krastev/spring-with-maven'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean install' //This is for building project with maven
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test' //Testing the maven modules
            }
        }
        stage('Docker login'){
            steps {
               sh 'docker login -u $DOCKERHUB_CREDENTIALS_USR -p $DOCKERHUB_CREDENTIALS_PSW'
            }

        }

        stage('Docker build and tag') {
            steps {
                sh 'docker build -t ${IMAGE_NAME} -f Dockerfile .'
                sh 'docker tag ${IMAGE_NAME} ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }

        stage('Docker Push') {
            steps {
                sh 'docker push ${IMAGE_NAME}:${BUILD_NUMBER}'
            }
        }
        stage('Deploy') {
           steps {
                sh 'pkill node | true'
                sh 'npm install -g forever'
                sh 'maven install -g forever'
                sh 'forever start src/main/HelloWorldApplication.java'
           }
        }
    }
    post{                   // deletes repo after execution
            always {
                cleanWs()
            }
    }
}
