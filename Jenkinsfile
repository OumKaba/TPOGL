pipeline {
    agent any

    environment {
        SONARQUBE_TOKEN = 'token'  // Utilisez un token Jenkins configuré dans les paramètres du job si nécessaire
        MAVEN_REPO_URL = 'https://mymavenrepo.com/repo/wfeEoJVTqyCrSb3fpohC/'
        MAVEN_REPO_USERNAME = 'myMavenRepo'
        MAVEN_REPO_PASSWORD = '12345678'
    }

    stages {
        stage('Checkout') {
            steps {
                // Vérifie le code depuis le dépôt Git
                checkout scm
            }
        }

        stage('Test') {
            steps {
                // Exécuter les tests
                script {
                    bat './gradlew test'
                }
            }
            post {
                always {
                    // Assurez-vous que le chemin des rapports de test est correct
                    junit '**/build/test-logs/*.xml'  // Vérifiez le chemin des rapports de test
                }
            }
        }

        stage('Code Analysis (SonarQube)') {
            steps {
                // Lancer l'analyse SonarQube via la configuration Jenkins
                script {
                    bat './gradlew sonarqube -Dsonar.login=${SONARQUBE_TOKEN}'
                }
            }
        }

        stage('Code Quality') {
            steps {
                script {
                    // Vérifier l'état du Quality Gate de SonarQube
                    def qualityGateStatus = bat(script: './gradlew sonarqube -Dsonar.login=${SONARQUBE_TOKEN} -Dsonar.qualitygate.wait=true', returnStatus: true)
                    if (qualityGateStatus != 0) {
                        currentBuild.result = 'FAILURE'
                        error "Quality Gate failed!"
                    }
                }
            }
        }

        stage('Build') {
            steps {
                // Compiler et construire le projet (générer le JAR et la documentation)
                script {
                    bat './gradlew build generateJavadoc'
                }
            }
            post {
                success {
                    // Archivage du fichier JAR et de la documentation
                    archiveArtifacts artifacts: '**/build/libs/*.jar', allowEmptyArchive: true
                    archiveArtifacts artifacts: '**/build/docs/javadoc/**/*', allowEmptyArchive: true
                }
            }
        }

        stage('Deploy') {
            steps {
                // Déployer le fichier JAR dans le dépôt Maven
                script {
                    bat './gradlew publish'
                }
            }
        }

        stage('Notification') {
            steps {
                script {
                    // Envoyer des notifications Slack
                    slackSend (channel: '#notification', message: "Build and deploy completed successfully. Version: ${env.BUILD_TAG}")

                    // Envoyer un email en cas de succès
                    mail to: 'lo_kaba@esi.dz', subject: "Build Success", body: "The build and deployment have been successfully completed!"
                }
            }
        }
    }

    post {
        failure {
            // Envoyer une notification Slack en cas d'échec
            slackSend (channel: '#notification', message: "Build or deploy failed. Please check the logs.")

            // Envoyer un email en cas d'échec
            mail to: 'lo_kaba@esi.dz', subject: "Build Failure", body: "The build or deployment has failed. Please check the Jenkins logs for details."
        }
    }
}