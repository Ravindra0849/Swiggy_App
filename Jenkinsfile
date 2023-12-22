pipeline {
    agent any
    
    tools {
        jdk "jdk17"
        nodejs "node16"
    }
    
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    
    stages {
        stage ("Clean Workspace") {
            steps {
                cleanWs()
            }
        }
        
        stage ("Git Checkout Code") {
            steps {
                git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/Ravindra0849/Swiggy_App.git'
            }
        }
        
        stage ("Sonar Scan") {
            steps {
                withSonarQubeEnv('sonar') {
                    sh " $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=swiggy -Dsonar.projectKey=swiggy "
                }
            }
        }
        
        stage('Quality Gates Checking') {
            steps {
                waitForQualityGate abortPipeline: false, credentialsId: 'sonar'
            }
        }
        
        stage('Npm Dependency') {
            steps {
                sh "npm install"
            }
        }
        
        stage('Trivy Scan') {
            steps {
                sh "trivy fs . > trivyfs.txt "
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){   
                        sh "docker build -t swiggy ."
                        sh "docker tag swiggy ravisree900/swiggy:'${env.BUILD_NUMBER}'"
                    }
                }
            }
        }
        
    }
}