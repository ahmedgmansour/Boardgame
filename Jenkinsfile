pipeline{
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }   
    environment {
        DOCKER_IMAGE_NAME = "ahmedgmansour/boardgame"
    }
    stages {
        stage('init') {
            steps {
                script {
                    if (env.BRANCH_NAME == "master") {
                        DOCKER_IMAGE_NAME = "ahmedgmansour/drupal"
                        // NAMESPACE = ""
                    }
                    else if (env.BRANCH_NAME == "diamond")
                    {
                        DOCKER_IMAGE_NAME = "ahmedgmansour/drupal-test"
                        // NAMESPACE = "test"
                    }
                }
            }
        }                    
        stage('clean workspace'){
            steps{
                cleanWs()
            }
        }
        stage('Checkout from Git'){
            steps{
                git branch: 'main', url: 'https://github.com/ahmedgmansour/Boardgame.git'
            }
        }
        stage('Compile') {
            steps {
                sh "mvn compile"
            }
        }
        
        stage('Test') {
            steps {
                sh "mvn test"
            }
        }
        stage('Build') {
            steps {
               sh "mvn package"
            }
        }        
        stage("Docker Build & Push"){
            steps{
                script{
                   withDockerRegistry(credentialsId: 'docker', toolName: 'docker'){   
                      sh """
                        echo ${DOCKER_IMAGE_NAME}
                        docker build -t ${DOCKER_IMAGE_NAME} .
                        docker push ${DOCKER_IMAGE_NAME}:latest
                        """
                    }
                }
            }
        }
        stage('Deploy to container'){
            steps{
                sh 'docker run -d --name drupal -p 8080:80 ${DOCKER_IMAGE_NAME}:latest'
            }
          } 
        } 
    }

 
  
