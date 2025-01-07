pipeline {
    agent any
    environment {
        SONARQUBE = 'sonar'  // Nom de votre serveur SonarQube configuré dans Jenkins
        SONAR_PROJECT_NAME = 'oumayma_tp7'  // Nom de votre projet SonarQube
        SONAR_PROJECT_KEY = 'oumayma_tp7'  // Clé unique de votre projet SonarQube
    }

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

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Lancer l'analyse SonarQube en utilisant le plugin Jenkins SonarQube
                    withSonarQubeEnv('sonar') {  // Utiliser le serveur SonarQube configuré
                        bat "./gradlew.bat sonarqube -Dsonar.projectName=${SONAR_PROJECT_NAME} -Dsonar.projectKey=${SONAR_PROJECT_KEY}"
                    }
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

        /*
        stage('Send Email') {
            steps {
                script {
                    echo "Sending email..."
                    sendEmail()
                }
            }
        }
        */
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
