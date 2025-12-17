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
                sh 'mvn clean compile -DskipTests'
            }
        }

        stage('📦 Create JAR') {
            steps {
                echo "Packaging du projet..."
                sh 'mvn package -DskipTests' 
                sh 'ls -la target/*.jar'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                script {
                    withSonarQubeEnv('devops') {
                        sh 'mvn clean verify sonar:sonar -DskipTests'
                    }
                }
            }
        }
    } // Fin de stages
} // Fin de pipeline
