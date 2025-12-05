pipeline {
    agent any

    tools {
        // Assurez-vous que le nom 'Maven-3.9.11' correspond au nom configuré dans Gérer Jenkins -> Outils globaux
        maven 'Maven-3.9.11'
    }

    environment {
        // Assurez-vous que 'SONARQUBE_TOKEN' est le nom exact de votre identifiant (Credential ID) stocké dans Jenkins
        SONAR_TOKEN = credentials('SONARQUBE_TOKEN')  
    }

    stages {
        stage('1️⃣ Clone Repository') {
            steps {
                echo '📥 Clonage du repository Git...'
                git branch: 'main', url: 'https://github.com/hassenjridi12/Mangement-student.git'
                echo '✅ Clonage terminé'
            }
        }

        stage('2️⃣ Build Project') {
            steps {
                echo '🔨 Compilation du projet avec Maven...'
                // Utilisation de cmd au lieu de bat pour les commandes simples sur Windows
                // Si votre agent est Linux, remplacez 'bat' par 'sh'
                bat 'mvn clean compile -DskipTests' 
                echo '✅ Build terminé'
            }
        }

        stage('3️⃣ Run Tests') {
            steps {
                echo '🧪 Exécution des tests...'
                bat 'mvn test'
                echo '✅ Tests terminés'
            }
        }

        stage('4️⃣ Package JAR') {
            steps {
                echo '📦 Packaging du projet en JAR...'
                bat 'mvn package -DskipTests'
                echo '✅ Package JAR terminé'
            }
        }

        stage('5️⃣ SonarQube Analysis') {
            steps {
                echo '🔍 Analyse de qualité du code avec SonarQube...'
                // Le nom 'sonarqube' DOIT correspondre au nom configuré dans 
                // Gérer Jenkins -> Configurer le système -> SonarQube servers
                withSonarQubeEnv('sonarqube') {
                    // Le plugin SonarQube va automatiquement injecter l'URL et le token.
                    bat """
                    mvn sonar:sonar ^
                        -Dsonar.projectKey=student-management ^
                        -DskipTests
                    """
                }
                echo '✅ Analyse SonarQube terminée'
            }
        }

        stage('6️⃣ Archive Artifact') {
            steps {
                echo '📁 Archivage du fichier JAR...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                echo '✅ Archivage terminé'
            }
        }
    }

    post {
        failure {
            echo '❌ Le pipeline a échoué'
        }
        success {
            echo '🎉 Pipeline terminé avec succès'
        }
    }
}
