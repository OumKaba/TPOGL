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
                        echo 'Running unit tests...'
                        script {
                            try {
                                bat './gradlew test'
                                junit '**/build/test-results/test/*.xml'
                                cucumber '**/reports/*.json'
                            } catch (Exception e) {
                                echo "Test stage failed: ${e.message}"
                                currentBuild.result = 'FAILURE'
                                error("Test stage failed")
                            }
                        }
                    }
                }

                stage('Code Analysis') {
                    steps {
                        echo 'Running SonarQube analysis...'
                        script {
                            withSonarQubeEnv('sonar') {
                                bat './gradlew sonar'
                            }
                        }
                    }
                }

                stage('Code Quality') {
                    steps {
                        echo 'Checking SonarQube Quality Gates...'
                        script {
                            try {
                                timeout(time: 3, unit: 'MINUTES') {
                                    def qg = waitForQualityGate()


                                    if (qg.status != 'OK') {
                                        echo "Quality Gates failed: ${qg.status}"
                                        currentBuild.result = 'UNSTABLE' // Mark as unstable instead of failing
                                        error("Quality Gates failed. Stopping pipeline.")
                                    } else {
                                        echo "Quality Gates passed: ${qg.status}"
                                    }
                                }
                            } catch (Exception e) {
                                echo "Quality Gates check failed: ${e.message}"
                                currentBuild.result = 'FAILURE'
                                error("Quality Gates check failed")
                            }
                        }
                    }
                }

                stage('Build') {
                    steps {
                        echo 'Building the project...'
                        script {
                            try {
                                bat './gradlew build'
                                archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
                            } catch (Exception e) {
                                echo "Build stage failed: ${e.message}"
                                currentBuild.result = 'FAILURE'
                                error("Build stage failed")
                            }
                        }
                    }
                }

                stage('Deployy') {
                    steps {
                        echo 'Deploying to MyMavenRepo...'
                        bat "./gradlew publish"
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
