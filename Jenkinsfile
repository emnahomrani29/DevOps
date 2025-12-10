pipeline {
    agent any

    stages {
        stage('Checkout Git') {
            steps {
                script {
                    git credentialsId: 'github-credentials', 
                        branch: 'main', 
                        url: 'https://github.com/emnahomrani29/DevOps.git'
                }
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t emnahomrani/student-management:latest -f Dockerfile .'
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', 
                                     usernameVariable: 'DOCKER_USER', 
                                     passwordVariable: 'DOCKER_PASS')]) {
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        sh 'docker push emnahomrani/student-management:latest'
                    }
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'jenkins-sonar', variable: 'SONAR_TOKEN')]) {
                    sh 'mvn sonar:sonar -Dsonar.login=$SONAR_TOKEN'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Appliquer les fichiers Kubernetes (MySQL + Spring)
                    sh 'kubectl apply -f mysql-deployment.yaml'
                    sh 'kubectl apply -f spring-deployment.yaml'
                }
            }
        }
    }

    post {
        success {
            emailext (
                subject: "SUCCESS: Pipeline ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le build a réussi ! Voir les détails : ${env.BUILD_URL}",
                to: "tonemail@gmail.com"
            )
        }
        failure {
            emailext (
                subject: "FAILED: Pipeline ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le build a échoué. Voir les détails : ${env.BUILD_URL}",
                to: "tonemail@gmail.com"
            )
        }
    }
}
