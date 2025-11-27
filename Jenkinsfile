pipeline {
    agent any

    tools {
        maven 'Maven-3.9.11'
    }

    stages {

        stage('Checkout') {
            steps {
                echo "🎉 Étape 1: Préparation de l'environnement"
                bat "echo Checkout OK"
            }
        }

        stage('Build') {
            steps {
                bat "mvn clean package"
            }
        }

        stage('Test') {
            steps {
                bat "echo Tests OK"
            }
        }

        stage('Deploy') {
            steps {
                bat "echo Déploiement OK"
            }
        }
    }

    post {
        always {
            echo "✔️ Pipeline terminé!"
        }
    }
}
