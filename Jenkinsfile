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
                    // Exécution de la commande Gradle build
                    sh './gradlew build'
                }
            }
        }

        stage('Test') {
            steps {
                echo "Running tests..."
                script {
                    // Exécution de la commande Gradle test
                    sh './gradlew test'
                }
            }
        }

        stage('Publish') {
            steps {
                echo "Publishing the build..."
                script {
                    // Publier le build ou effectuer des actions supplémentaires comme l'envoi de mails ou notification
                    sh './gradlew publish'
                }
            }
        }

        stage('Notify Success') {
            steps {
                echo "Sending success notifications..."
                script {
                    // Ici, vous pouvez activer ou désactiver l'envoi de notifications Slack ou d'emails, en fonction de vos besoins.
                    // Par exemple, appeler le task sendMailCustom pour envoyer l'email de notification
                    sh './gradlew sendMailCustom'
                }
            }
        }

        stage('Post Build') {
            steps {
                echo "Post Build Actions..."
                script {
                    // Vous pouvez également ajouter d'autres actions de post-build, comme la publication de résultats ou l'exécution de tests supplémentaires.
                    // Exemple : notification Slack (commenté par défaut ici)
                    // sh './gradlew postPublishedPluginToSlack'
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
