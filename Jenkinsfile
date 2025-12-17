pipeline {
    agent any

    stages {
        stage('📥 Git Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }

        stage('🔍 Diagnostic (List Files)') {
            steps {
                echo "Liste des fichiers présents dans le workspace :"
                // Cette commande affiche tous les fichiers pour trouver le Dockerfile
                bat "dir /s /b" 
            }
        }

        stage('🐳 Docker Build') {
            steps {
                script {
                    // Si votre fichier s'appelle 'dockerfile' (minuscule) ou est dans un dossier,
                    // il faudra modifier la ligne ci-dessous.
                    bat "docker build -t hassenjridi12/management-student:latest ."
                }
            }
        }
    }

    post {
        failure {
            echo '❌ Le Dockerfile est introuvable ou mal nommé.'
        }
    }
}
