pipeline {
    agent any  // Utiliser n'importe quel agent disponible

    environment {
        // Définition des variables d'environnement si nécessaire
        SMTP_HOST = 'smtp.gmail.com'
        SMTP_PORT = '587'
        USERNAME = 'lo_kaba@esi.dz'
        PASSWORD = 'msvh ybei sfjf jjlb'  // Assurez-vous de sécuriser les identifiants
        FROM_EMAIL = 'lo_kaba@esi.dz'
        TO_EMAIL = 'lr_soltani@esi.dz'
        EMAIL_SUBJECT = 'Notification Mail'
        EMAIL_BODY = 'Hello!! This is the last update'
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    // Récupérer le code source du dépôt
                    git 'https://github.com/OumKaba/TPOGL'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Exécution de la tâche Gradle pour la construction sous Windows
                    bat './gradlew.bat build'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Exécution des tests via Gradle
                    bat './gradlew.bat test'
                }
            }
        }

        stage('Publish') {
            steps {
                script {
                    // Publication du code via Gradle
                    bat './gradlew.bat publish'
                }
            }
        }

        stage('Send Email') {
            steps {
                script {
                    // Configuration et envoi du mail
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

def sendEmail() {
    // Configuration du serveur SMTP pour l'envoi d'email
    def props = new Properties()
    props.put("mail.smtp.auth", "true")
    props.put("mail.smtp.starttls.enable", "true")
    props.put("mail.smtp.host", SMTP_HOST)
    props.put("mail.smtp.port", SMTP_PORT)

    // Création de la session d'authentification
    def session = javax.mail.Session.getInstance(props, new javax.mail.Authenticator() {
        @Override
        protected javax.mail.PasswordAuthentication getPasswordAuthentication() {
            return new javax.mail.PasswordAuthentication(USERNAME, PASSWORD)
        }
    })

    try {
        // Création du message et envoi
        def message = new javax.mail.internet.MimeMessage(session)
        message.setFrom(new javax.mail.internet.InternetAddress(FROM_EMAIL))
        message.setRecipients(javax.mail.Message.RecipientType.TO, javax.mail.internet.InternetAddress.parse(TO_EMAIL))
        message.setSubject(EMAIL_SUBJECT)
        message.setText(EMAIL_BODY)
        javax.mail.Transport.send(message)
        echo "Email sent successfully to: $TO_EMAIL"
    } catch (Exception e) {
        echo "Failed to send email: ${e.message}"
    }
}
