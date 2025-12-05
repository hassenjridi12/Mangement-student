pipeline {
    agent any

    tools {
        maven 'Maven-3.9.11'
    }

    environment {
        SONAR_URL = "http://localhost:9000"
        SONAR_LOGIN = "admin"
        SONAR_PASSWORD = "sonar"
        SONAR_PROJECT_KEY = "MonProjetJava"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "🎉 Étape 1: Préparation de l'environnement"
                bat "echo Checkout OK"
            }
        }

        stage('Clean') {
            steps {
                echo "🧹 Nettoyage du dossier target"
                bat "if exist target rmdir /s /q target"
            }
        }

        stage('Build') {
            steps {
                echo "🔨 Build du projet avec Maven"
                bat "mvn clean package -DskipTests=true"
            }
        }

        stage('Test') {
            steps {
                echo "🧪 Tests ignorés pour le moment"
                bat "echo Tests skipped"
            }
        }

        stage('SonarQube Analysis') {
            steps {
                echo "🔍 Analyse SonarQube du code source"
                bat """
                    mvn sonar:sonar ^
                    -Dsonar.projectKey=%SONAR_PROJECT_KEY% ^
                    -Dsonar.host.url=%SONAR_URL% ^
                    -Dsonar.login=%SONAR_LOGIN% ^
                    -Dsonar.password=%SONAR_PASSWORD%
                """
            }
        }

        stage('Deploy') {
            steps {
                echo "🚀 Déploiement simulé"
                bat "echo Deploy OK"
            }
        }
    }

    post {
        always {
            echo "✔️ Pipeline terminé !"
        }
    }
}
