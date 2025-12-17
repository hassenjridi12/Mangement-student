pipeline {
    agent any

    tools {
        maven 'Maven-3.9.11'      // Nom EXACT dans Jenkins
        jdk 'JAVA_HOME'      // Nom EXACT dans Jenkins
    }

    stages {

        stage('1️⃣ Clone Repository') {
            steps {
                echo '📥 Clonage du repository Git...'
                git branch: 'main',
                    url: 'https://github.com/hassenjridi12/Mangement-student.git'
                echo '✅ Clonage terminé'
            }
        }

        stage('2️⃣ Build Project') {
            steps {
                echo '🔨 Compilation du projet avec Maven...'
                bat 'mvn clean compile -DskipTests'
                echo '✅ Build terminé'
            }
        }

        stage('3️⃣ Package Project') {
            steps {
                echo '📦 Packaging du projet...'
                bat 'mvn package -DskipTests'
                echo '✅ Packaging terminé'
            }
        }

        stage('5️⃣ Package JAR') {
            steps {
                echo '📦 Packaging final en JAR...'
                bat 'mvn clean package -DskipTests'
                echo '✅ JAR prêt'
            }
        }

        stage('6️⃣ Archive Artifact') {
            steps {
                echo '📁 Archivage du fichier JAR...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('7️⃣ Build Docker Image') {
            steps {
                echo '🐳 Construction de l’image Docker...'
                bat 'docker build -t student-management:1.0 .'
                echo '✅ Image Docker créée'
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )]) {
                        bat '''
                        echo %DOCKERHUB_PASS% | docker login -u %DOCKERHUB_USER% --password-stdin
                        docker tag student-management:1.0 %DOCKERHUB_USER%/student-management:1.0
                        docker push %DOCKERHUB_USER%/student-management:1.0
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo '🎉 Pipeline terminé avec succès'
        }
        failure {
            echo '❌ Le pipeline a échoué'
        }
    }
}
