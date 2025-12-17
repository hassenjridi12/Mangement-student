pipeline {
    agent any

    tools {
        maven 'Maven-3.9.11'
        jdk 'JAVA_HOME'
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
            }
        }

        stage('3️⃣ Package Project') {
            steps {
                echo '📦 Packaging du projet...'
                bat 'mvn package -DskipTests'
            }
        }

        stage('4️⃣ Archive Artifact') {
            steps {
                echo '📁 Archivage du JAR...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }

        stage('5️⃣ Build Docker Image (OPTIONNEL)') {
            steps {
                script {
                    echo '🐳 Vérification Docker...'
                    def dockerOk = bat(
                        script: 'docker version',
                        returnStatus: true
                    )

                    if (dockerOk == 0) {
                        echo '✅ Docker disponible'
                        bat 'docker build -t student-management:1.0 .'
                    } else {
                        echo '⚠️ Docker indisponible – étape ignorée'
                    }
                }
            }
        }

        stage('6️⃣ Push Docker Image (OPTIONNEL)') {
            when {
                expression {
                    bat(script: 'docker version', returnStatus: true) == 0
                }
            }
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
