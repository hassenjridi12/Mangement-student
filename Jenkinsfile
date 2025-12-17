pipeline {
    agent any

    environment {
        // Remplacez par votre identifiant Docker Hub
        DOCKER_USER = 'hassenjridi12'
        IMAGE_NAME  = 'management-student'
    }

    stages {
        stage('📥 Git Clone') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }

        stage('🐳 Docker Build & Push') {
            steps {
                script {
                    echo "Construction de l'image Docker..."
                    // Construction de l'image
                    bat "docker build -t ${DOCKER_USER}/${IMAGE_NAME}:latest ."
                    
                    echo "Connexion à Docker Hub..."
                    // Note: Il est préférable d'utiliser des credentials Jenkins pour le login
                    // bat "docker login -u user -p password" 
                    
                    echo "Push de l'image..."
                    bat "docker push ${DOCKER_USER}/${IMAGE_NAME}:latest"
                }
            }
        }
    }
}
