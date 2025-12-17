pipeline {
    agent any

    stages {
        stage('📥 Git Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }

        stage('🏗️ Build') {
            steps {
                echo "Compilation du projet..."
                // Utilisation de 'bat' au lieu de 'sh' pour Windows
                bat 'mvn clean compile -DskipTests'
            }
        }

        stage('📦 Create JAR') {
            steps {
                echo "Packaging du projet..."
                bat 'mvn package -DskipTests' 
                bat 'dir target\\*.jar' // 'dir' est l'équivalent de 'ls' sur Windows
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('devops') {
                        bat 'mvn clean verify sonar:sonar -DskipTests'
                    }
                }
            }
        }
    }
}
