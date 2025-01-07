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
                            bat './gradlew test'
                        }
                    }
                }
        stage('SonarQube Analysis') {
                    steps {
                        script {
                            // Lancer l'analyse SonarQube en utilisant le plugin Jenkins SonarQube
                            withSonarQubeEnv('sonar') {  // Utiliser l'environnement SonarQube configuré
                                bat './gradlew sonarqube '
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
            bat './gradlew sendMailCustom -PemailBody="Déploiement terminé"  -PemailTo="lo_kaba@esi.dz"'
            bat './gradlew postpublishedPluginToSlack -PslackMessage="Déploiement terminé"'
          }
        }


}
}