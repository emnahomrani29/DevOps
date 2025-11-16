pipeline {
    agent any
    tools {
        maven 'Maven'
    }
    environment {
        APP_ENV = "DEV"
    }
    stages {
        stage('Code Checkout') {
            steps {
                git branch: 'master',
                    url: 'https://github.com/emnahomrani29/DevOps.git'
            }
        }
        stage('Code Build') {
            steps {
                sh 'mvn -version'
            }
        }
    }
    post {
        always { echo "======always======" }
        success { echo "=====pipeline executed successfully =====" }
        failure { echo "======pipeline execution failed======" }
    }
}
