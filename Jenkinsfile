pipeline {
    agent any

    environment {
        JAVA_HOME = tool 'JDK 11'
        GRADLE_HOME = tool 'Gradle 8.8'
        PATH = "${GRADLE_HOME}/bin:${JAVA_HOME}/bin:${env.PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                echo "Checking out the source code..."
                checkout scm
            }
        }

        stage('Build') {
            steps {
                echo "Building the project..."
                script {
                    // Utilisez bat pour exécuter Gradle sous Windows
                    bat './gradlew.bat build'
                }
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                script {
                    // Utilisez bat pour exécuter Gradle sous Windows
                    bat './gradlew.bat test'
                }
            }
        }

        stage('Publish') {
            steps {
                echo "Publishing the build..."
                script {
                    // Publier le build ou effectuer des actions supplémentaires comme l'envoi de mails ou notification
                    bat './gradlew.bat publish'
                }
            }
        }

        stage('Notify Success') {
            steps {
                echo "Sending success notifications..."
                script {
                    // Exemple d'envoi d'email ou notification
                    bat './gradlew.bat sendMailCustom'
                }
            }
        }

        stage('Post Build') {
            steps {
                echo "Post Build Actions..."
                script {
                    // Actions supplémentaires post-build
                    // Exemple : notification Slack (commenté par défaut ici)
                    // bat './gradlew.bat postPublishedPluginToSlack'
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
            echo 'Cleaning up...'
            cleanWs()
        }
    }
}
