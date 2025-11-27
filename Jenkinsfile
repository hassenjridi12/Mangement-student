pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo "🎉 Étape 1: Préparation de l'environnement"
                bat 'echo Checkout OK'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                bat 'echo Deploiement terminé'
            }
        }
    }

    post {
        always {
            echo "✔️ Pipeline terminé!"
        }
    }
}
