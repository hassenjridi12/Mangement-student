pipeline {
    agent any

    tools {
        maven 'Maven-3.9.11' // Nom configuré dans Jenkins Global Tool Configuration
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
                bat "rmdir /s /q target"
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
                echo "🔍 Analyse SonarQube du code"
                bat """
                    mvn clean verify sonar:sonar ^
                    -Dsonar.host.url=http://localhost:9000 ^
                    -Dsonar.login=admin ^
                    -Dsonar.password=sonar
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
            echo "✔️ Pipeline terminé!"
        }
    }
}
