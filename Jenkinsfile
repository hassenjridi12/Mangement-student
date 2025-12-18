pipeline {

    agent any

    tools {
        maven 'Maven-3.9.11'
        jdk 'JAVA_HOME'
    }

    environment {
        APP_NAME = 'student-management'
        IMAGE_TAG = '1.0'
    }

    stages {

        stage('1️⃣ Checkout Source Code') {
            steps {
                echo '📥 Clonage du dépôt Git...'
                git branch: 'main',
                    url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }

        stage('2️⃣ Clean Project') {
            steps {
                echo '🧹 Nettoyage du projet...'
                bat 'mvn clean'
            }
        }

        stage('3️⃣ Compile Project') {
            steps {
                echo '⚙️ Compilation du projet...'
                bat 'mvn compile'
            }
        }

        stage('4️⃣ Package JAR') {
            steps {
                echo '📦 Packaging du JAR...'
                bat 'mvn package -DskipTests'
            }
        }

        stage('5️⃣ Archive Artifact') {
            steps {
                echo '📁 Archivage du JAR...'
                archiveArtifacts artifacts: 'target\\*.jar', fingerprint: true
            }
        }

        stage('6️⃣ Build Docker Image') {
            steps {
                echo '🐳 Construction de l’image Docker...'
                bat 'docker build -t %APP_NAME%:%IMAGE_TAG% .'
            }
        }

        stage('7️⃣ Push Docker Image to DockerHub') {
            steps {
                echo '🚀 Push vers Docker Hub...'
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )]) {
                        bat '''
                        docker login -u %DOCKERHUB_USER% -p %DOCKERHUB_PASS%
                        docker tag %APP_NAME%:%IMAGE_TAG% %DOCKERHUB_USER%/%APP_NAME%:%IMAGE_TAG%
                        docker push %DOCKERHUB_USER%/%APP_NAME%:%IMAGE_TAG%
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo '✅ Pipeline terminé avec succès 🎉'
        }
        failure {
            echo '❌ Pipeline échoué'
        }
    }
}
