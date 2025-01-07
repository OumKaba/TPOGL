pipeline {
    agent any

    stages {
        stage('Check Environment') {
            steps {
                script {
                    echo "Java Version:"
                    bat 'java -version'  // Windows
                    echo "Gradle Version:"
                    bat './gradlew -v'   // Windows
                }
            }
        }

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                script {
                    bat './gradlew build'  // Pour Windows
                }
            }
        }
    }
}
