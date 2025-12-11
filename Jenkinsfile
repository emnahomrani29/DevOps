pipeline {
    agent any
    
    tools {
        maven 'Maven'
        jdk   'JDK17'
    }
    
    stages {
        stage('Checkout Git') {
            steps {
                echo '📥 Cloning repository...'
                git credentialsId: 'github-credentials', 
                    branch: 'master', 
                    url: 'https://github.com/emnahomrani29/DevOps.git'
            }
        }
        
        stage('Build with Maven') {
            steps {
                echo '📦 Building with Maven...'
                sh 'mvn clean install -DskipTests'
            }
        }
        
        stage('SonarQube Analysis') {
            steps {
                echo '🔍 Analyzing code quality...'
                script {
                    withSonarQubeEnv('sonarqube-local') {
                        sh 'mvn sonar:sonar -Dsonar.projectKey=student-management'
                    }
                }
            }
        }
        
        stage('Build Docker Image') {
            steps {
                echo '🐳 Building Docker image...'
                sh 'docker build -t emnahomrani/student-management:latest -f Dockerfile .'
            }
        }
        
        stage('Push to Docker Hub') {
            steps {
                echo '📤 Pushing to Docker Hub...'
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'docker-hub-credentials', 
                        usernameVariable: 'DOCKER_USER', 
                        passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh 'docker push emnahomrani/student-management:latest'
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Pipeline successful!'
        }
        failure {
            echo '❌ Pipeline failed!'
        }
    }
}
