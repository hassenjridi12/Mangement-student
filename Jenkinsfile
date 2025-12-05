pipeline {
    agent any

    stages {

        stage('📥 Git Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/chahinesaadellaoui/ProjetDevops.git'
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
        withSonarQubeEnv(installationName: 'devops') {
            sh 'mvn clean verify sonar:sonar -DskipTests'
        }
    }
}

    }

    post {
        success {
            echo '🎉 Pipeline exécuté avec succès !'
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
        failure {
            echo '❌ Échec du pipeline.'
        }
    }
}
