pipeline {

    agent any

    tools {
        maven 'Maven-3.9.11'     // Nom EXACT dans Jenkins
        jdk 'JAVA_HOME'          // Nom EXACT dans Jenkins
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
                    url: 'https://github.com/TON_USER_GITHUB/student-management.git'
            }
        }

        stage('2️⃣ Clean Project') {
            steps {
                echo '🧹 Nettoyage du projet...'
                sh 'mvn clean'
            }
        }

        stage('3️⃣ Compile Project') {
            steps {
                echo '⚙️ Compilation du projet...'
                sh 'mvn compile'
            }
        }

        stage('4️⃣ Run Tests (Skipped)') {
            steps {
                echo '⏭️ Tests ignorés'
            }
        }

        stage('5️⃣ Package JAR') {
            steps {
                echo '📦 Packaging du JAR...'
                sh 'mvn package -DskipTests'
            }
        }

        stage('6️⃣ Archive Artifact') {
            steps {
                echo '📁 Archivage du JAR...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('7️⃣ Build Docker Image') {
            steps {
                echo '🐳 Construction de l’image Docker...'
                sh '''
                    docker build -t ${APP_NAME}:${IMAGE_TAG} .
                '''
            }
        }

        stage('8️⃣ Push Docker Image to DockerHub') {
            steps {
                echo '🚀 Push vers Docker Hub...'
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )]) {
                        sh '''
                            echo "$DOCKERHUB_PASS" | docker login -u "$DOCKERHUB_USER" --password-stdin
                            docker tag ${APP_NAME}:${IMAGE_TAG} $DOCKERHUB_USER/${APP_NAME}:${IMAGE_TAG}
                            docker push $DOCKERHUB_USER/${APP_NAME}:${IMAGE_TAG}
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
        always {
            echo '📌 Fin du pipeline'
        }
    }
}
