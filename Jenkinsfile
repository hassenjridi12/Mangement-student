pipeline {
    agent any

    environment {
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
                    // Test si Docker répond bien
                    bat 'docker version'
                    
                    echo "Construction de l'image Docker..."
                    bat "docker build -t ${DOCKER_USER}/${IMAGE_NAME}:latest ."
                    
                    echo "Push de l'image..."
                    // Note: Assurez-vous d'être déjà connecté via 'docker login' sur la machine
                    bat "docker push ${DOCKER_USER}/${IMAGE_NAME}:latest"
                }
            }
        }
    }
}
