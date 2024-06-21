pipeline {
    agent any
    
    environment{
        SCANNER_HOME = tool 'sonar-scanner'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/KollaChandini/valentine-day-project.git'
            }
        }
        
        stage('Trivy FileSystem Scan') {
            steps {
                sh 'trivy fs --format table -o trivy-fs-report.html .'
            }
        }
        
        stage('SoanrQube Analysis') {
            steps {
                withSonarQubeEnv('sonar-server') {
                    sh '''
                    $SCANNER_HOME/bin/sonar-scanner \
                    -Dsonar.projectKey=valentine \
                    -Dsonar.projectName=valentine '''
                    
                }
            }
        }
        
        stage('Build & Tag Docker Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker build -t kollachandini/valentine:v1 .'
                    }
                }
            }
        }
        
        
        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image --format json -o trivy-image-report.json kollachandini/valentine:v1'
            }
        }
        
        stage('Push Docke Image') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker push kollachandini/valentine:v1'
                    }
                }
            }
        }
        
        stage('Deploy To Container') {
            steps {
                sh 'docker run -d -p 8081:80 kollachandini/valentine:v1'
            }
        }
    }
}
