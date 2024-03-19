pipeline {
    agent any
    
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "reddit-clone-app"
        RELEASE = "1.0.0"
        DOCKER_USER = "philcappalot"
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }
    
    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Checkout Git') {
            steps {
                git branch: 'main', url: 'https://github.com/philtebi10/lab-tf.git'
            }
        }
        
        stage('Install Dependencies') {
            steps {
                sh "npm install"
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQube-Server') {
                    sh "$SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.projectKey=reddit-clone-CI \
                        -Dsonar.projectName=Reddit-Clone-CI \
                        -Dsonar.sources=src \
                        -Dsonar.language=javascript \
                        -Dsonar.sourceEncoding=UTF-8"
                }
            }
        }
        
        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialId: 'SonarQube-Token'
                }
            }
        }
        
        stage('TRIVY FS SCAN') {
            steps {
                sh "trivy fs . > trivyfs.txt"
            }
        }
    }
}
