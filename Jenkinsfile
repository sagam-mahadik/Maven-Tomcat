pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'maven3'
    }
    environment {
        APP_NAME = "register-app-pipeline"
            RELEASE = "1.0.0"
            DOCKER_USER = "sagarmahadik"
            DOCKER_PASS = 'dockerhub-token'
            IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
            IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	    JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
    }
    stages{
        stage('cleanup workspace') {
            steps {
                cleanWs()
            }
        }
        stage('checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/sagam-mahadik/Maven-Tomcat.git'
            }
        }
        stage('Build application') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Application Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-tokan') {
                    sh "mvn sonar:sonar@123"
                    }
                }

            }
        }
        stage('Quality Gate') {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-tokan'
                }
            }
        } 
        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
       }  
    }
}
