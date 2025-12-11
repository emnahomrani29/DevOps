pipeline {
    agent any
    
    tools {
        maven 'Maven'
    }
    
    stages {
        stage('Checkout Git') {
            steps {
                echo '📥 Cloning repository...'
                git credentialsId: 'github-credentials', 
                    branch: 'main', 
                    url: 'https://github.com/emnahomrani29/DevOps.git'
            }
        }
        
        stage('Build with Maven') {
            steps {
                echo '📦 Building with Maven...'
                sh 'mvn clean install -DskipTests'
            }
        }
        
        stage('Run Tests') {
            steps {
                echo '🧪 Running tests...'
                sh 'mvn test'
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
        
       
    post {
        success {
            echo '✅ Pipeline successful!'
            emailext (
                subject: "✅ SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build réussi! Voir: ${env.BUILD_URL}",
                to: "emnahomrani29@gmail.com"
            )
        }
        failure {
            echo '❌ Pipeline failed!'
            emailext (
                subject: "❌ FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Build échoué. Voir: ${env.BUILD_URL}console",
                to: "emnahomrani29@gmail.com"
            )
        }
    }
}
