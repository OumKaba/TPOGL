pipeline {
    agent any
    environment {
        SONARQUBE = 'sonar'  // Nom de votre serveur SonarQube configuré dans Jenkins
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
                    // Exécution des tests unitaires
                    bat './gradlew test'

                    // Génération des rapports Cucumber après les tests
                    bat './gradlew generateCucumberReports'

                    // Affichage du rapport Cucumber généré
                    echo 'Cucumber report generated at: build/reports/cucumber/cucumber-html-reports/overview-features.html'
                }
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    // Lancer l'analyse SonarQube en utilisant le plugin Jenkins SonarQube
                    withSonarQubeEnv('sonar') {  // Utiliser l'environnement SonarQube configuré
                        bat './gradlew sonarqube'
                    }
                }
            }
        }

        stage('Code Quality') {
            steps {
                script {
                    // Attendre la fin de l'analyse SonarQube
                    timeout(time: 1, unit: 'HOURS') {
                        waitForQualityGate()  // Attend la fin de l'analyse et récupère l'état du Quality Gate
                    }

                    // Vérifie l'état du Quality Gate
                    def qualityGateStatus = currentBuild.result
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
                echo 'Send Notifications To the Team...'

                // Envoi d'un email personnalisé après le déploiement
                bat './gradlew sendMailCustom -PemailBody="Déploiement terminé" -PemailTo="lo_kaba@esi.dz"'

                // Envoi de la notification sur Slack
                bat './gradlew postpublishedPluginToSlack -PslackMessage="Déploiement terminé"'
            }
        }
    }
}
