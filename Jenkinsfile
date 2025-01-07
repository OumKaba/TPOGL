pipeline {
    agent any

    environment {
        // Définir les variables d'environnement pour SonarQube et les autres outils si nécessaire
        SONARQUBE_URL = 'http://197.140.142.82:9000/'
        SONARQUBE_TOKEN = 'token'
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
                    // Utilisez 'gradlew.bat' pour Windows
                    bat './gradlew.bat test'
                }
            }
            post {
                always {
                    // Assurez-vous que le chemin des rapports de test est correct
                    junit '**/build/test-logs/*.xml'  // Vérifiez le chemin de vos rapports de test
                }
            }
        }

        stage('Code Analysis (SonarQube)') {
            steps {
                // Lancer l'analyse SonarQube
                script {
                    bat './gradlew.bat sonarqube -Dsonar.login=${SONARQUBE_TOKEN}'
                }
            }
        }

        stage('Code Quality') {
            steps {
                script {
                    // Vérifier l'état du Quality Gate de SonarQube
                    def qualityGateStatus = bat(script: './gradlew.bat sonarqube -Dsonar.login=${SONARQUBE_TOKEN} -Dsonar.qualitygate.wait=true', returnStatus: true)
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
                    bat './gradlew.bat build generateJavadoc'
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
                    bat './gradlew.bat publish'
                }
            }
        }

        stage('Notification') {
            steps {
                script {
                    // Envoyer des notifications Slack
                    slackSend (channel: '#notification', message: "Build and deploy completed successfully. Version: ${env.BUILD_TAG}")

                    // Envoyer un email en cas de succès
                    mail to: 'lo_kaba@€ei.dz', subject: "Build Success", body: "The build and deployment have been successfully completed!"
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
