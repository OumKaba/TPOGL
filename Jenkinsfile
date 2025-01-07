pipeline {
    agent any
    environment {
        SONARQUBE = 'sonar'  // Nom de votre serveur SonarQube configuré dans Jenkins
        SLACK_CHANNEL = '#notification'  // Remplacez par le canal Slack de votre choix
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Utilise le dépôt et la branche définis dans la configuration du job Jenkins
            }
        }

        stage('Test') {
            steps {
                script {
                    bat './gradlew test'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('sonar') {
                        bat './gradlew sonarqube'
                    }
                }
            }
        }

        stage('Code Quality') {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        waitForQualityGate()  // Attendre la fin de l'analyse SonarQube
                    }
                    def qualityGateStatus = waitForQualityGate().status
                    if (qualityGateStatus == 'FAILED') {
                        error "Le Quality Gate a échoué. Le pipeline est interrompu."
                    }
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    bat './gradlew build'
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    bat './gradlew publish'
                }
            }
        }

        stage('Notification') {
            steps {
                script {
                    echo 'Envoi de la notification vers Slack...'

                    // Envoi d'une notification Slack
                    slackSend (
                        channel: SLACK_CHANNEL,
                        message: "Déploiement terminé avec succès ! 🎉",
                        color: 'good'  // Utilisez 'good' pour vert, 'warning' pour jaune, 'danger' pour rouge
                    )
                }
            }
        }
    }
}
