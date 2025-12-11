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
                sh 'docker build -t emnahomrani/student-management -f Dockerfile .'
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
    stage('Deploy to Kubernetes') {
                steps {
                    echo '🚀 Deploying application to Kubernetes...'
                    script {
                        // Vérifie la connexion au cluster (optionnel, mais bonne pratique)
                        sh 'kubectl cluster-info'

                        // --- 1. Déploiement de la Base de Données MySQL (Dépendance) ---
                        echo '💾 Applying MySQL Deployment...'
                        // Applique le fichier de déploiement et de service MySQL
                        sh 'kubectl apply -f mysql-deployment.yaml'

                        // Optionnel: Attendre que MySQL soit prêt avant de déployer l'application
                        // sh 'kubectl rollout status deployment/mysql-deployment-name --timeout=5m'

                        // --- 2. Déploiement de l'Application Spring Boot ---
                        echo '🌐 Applying Spring Boot Deployment...'
                        // Applique le fichier de déploiement et de service Spring Boot
                        sh 'kubectl apply -f springboot-deployment.yaml'

                        // Optionnel: Vérifier le statut du déploiement de l'application
                        sh 'kubectl rollout status deployment/student-management-deployment --timeout=5m'

                        echo 'Deployment completed. Services will be available shortly.'
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
