pipeline {
    agent any
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/shrikantdandge665/Valentine-Day-DevOps-Project.git'
            }
        }
        
        stage('trivy fsscan') {
            steps {
                sh "trivy fs --format table -o trivy-fs-report.html ."
            }
        }
        
        stage('Sonarqube analysis') {
            steps {
                script {
                withSonarQubeEnv('sonar') {
                 sh "$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=Valentine -Dsonar.projectName=Valentine"
             
                    }
                }
            }
        }
        
        stage('Build & tag') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker build -t shrikantdandge7/valentine:v1 ."
                    }
                }
            }
        }
        
        stage('trivy scan') {
            steps {
                sh "trivy image shrikantdandge7/valentine:v1 > trivy-report.txt"
            }
        }
        
        stage('Docker image push') {
            steps {
                script {
                    withDockerRegistry(credentialsId: 'docker-cred') {
                        sh "docker push shrikantdandge7/valentine:v1"
                    }
                }
            }
        }
        
        stage('Deploy to container') {
            steps {
                sh "docker run -d -p 8081:80 shrikantdandge7/valentine:v1"
            }
        }
    }
}
