pipeline {
    agent any

    stages {
        stage('📥 Git Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }
        
        // J'ajoute l'étape Docker ici car un pipeline ne peut pas être vide
        stage('🐳 Docker Build') {
            steps {
                bat "docker build -t hassenjridi12/management-student:latest ."
            }
        }
    } // <--- Il manquait cette accolade pour fermer 'stages'

    post {
        success {
            echo '🎉 Pipeline exécuté avec succès !'
            // Attention : archiveArtifacts ne fonctionnera que si vous avez un dossier target/ avec un JAR
            // archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
        failure {
            echo '❌ Échec du pipeline.'
        }
    }
}
