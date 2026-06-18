pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'maven3'
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
    }
}
