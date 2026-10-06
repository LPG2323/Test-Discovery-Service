pipeline {

    agent any 

    tools {
        maven 'Maven-3.9'
    }

    environment{

        IMAGE_NAME = 'maitrelpg/ds'

        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage (" Checkout "){
            steps {
                git branch: 'main',
                url: 'https://github.com/LPG2323/Test-Discovery-Service.git'
            }

        }

        stage (" BUILD JAR "){
            steps {
                dir('discovery-service'){
                    sh 'mvn package'
                }
            }
        }  

        stage (" RUN TEST"){
            steps{
                dir('discovery-service'){
                    sh 'mvn test'
                }
            }
        }

        stage (" Sonarqube Analysis "){
            steps{
                dir('discovery-service'){
                   withSonarQubeEnv('sonarqube-server'){
                    sh 'mvn sonar:sonar'
                   } 
                }
            }
        }

        stage (" Docker Image "){
            steps{
                dir('discovery-service'){
                    sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
                }
            }
        }

        stage (" Docker Push "){
            steps{
                withCredentials([
            usernamePassword(
                credentialsId: 'dockerhub-credentials',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_TOKEN'
            )
        ]) {
            sh '''
                echo "$DOCKER_TOKEN" | docker login -u "$DOCKER_USER" --password-stdin
                docker push $IMAGE_NAME:$IMAGE_TAG
            '''

            }

        }
        
    }
    }

 }
    