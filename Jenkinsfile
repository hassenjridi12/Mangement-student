pipeline {
    agent any

    stages {

        stage('📥 Git Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hassenjridi12/Mangement-student.git'
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
