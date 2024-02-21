pipeline {
    agent any
    
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git checkout') {
            steps {
                git 'https://github.com/vijayalakshmi1205/dear_teddy.git'
            }
        }
        
        stage('Trivy') {
            steps {
               sh "trivy fs --format table -o trivy-fs-report.html ." 
            }
        }
        
        stage('Sonarqube') {
            steps {
                withSonarQubeEnv('sonar') {
                         sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=dearteddy -Dsonar.projectName=dearteddy"   
                }
            }
        }
        
        stage('Build & tag Docker Image') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker build -t srivijayalakshmi/dearteddy:v1 ."
                    }
                }
            }
        }
        
        stage('Trivy Image Scan') {
            steps {
                sh "trivy image --format json -o trivy-image-report.json srivijayalakshmi/dearteddy:v1"
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                  withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                            sh "docker push srivijayalakshmi/dearteddy:v1 "  
                    }        
                }
            }
        }
        
        stage('Deploy To Container') {
            steps {
                sh "docker run -d -p 8081:80 srivijayalakshmi/dearteddy:v1"
            }
        }
    }
}
