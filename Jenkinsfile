pipeline {
    agent any

    // Utilise JDK et Maven configurés dans Jenkins
    tools {
        jdk 'JDK17'
        maven 'Maven'
    }

    stages {
        stage('Récupérer le code') {
            steps {
                echo 'Récupération du code depuis GitHub...'
                git branch: 'master',
                    url: 'https://github.com/emnahomrani29/DevOps.git',
                    credentialsId: 'github-emnah'
            }
        }

        stage('Compiler et générer le JAR') {
            steps {
                echo 'Exécution de mvn package...'
                sh 'mvn clean package'
            }
        }

        stage('Tests unitaires (Bonus)') {
            steps {
                echo 'Lancement des tests...'
                sh 'mvn test'
            }
        }
    }

    post {
        success {
            echo 'Build réussi ! Archivage du JAR...'
            archiveArtifacts artifacts: 'target/*.jar', allowEmptyArchive: false
        }
        failure {
            echo 'Échec du build.'
        }
    }
}
