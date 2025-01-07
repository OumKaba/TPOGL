pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Utilise le dépôt et la branche définis dans la configuration du job Jenkins
            }
        }

        stage('Build') {
            steps {
                script {
                    bat './gradlew.bat build'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    bat './gradlew.bat test'
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    bat './gradlew.bat publish'
                }
            }
        }

        stage('Send Email') {
            steps {
                script {
                    echo "Sending email..."
                    sendEmail()
                }
            }
        }
    }

    post {
        success {
            echo 'Build was successful!'
        }
        failure {
            echo 'Build failed. Please check the logs.'
        }
        always {
            cleanWs()  // Nettoyer l'espace de travail après chaque exécution
        }
    }
}
