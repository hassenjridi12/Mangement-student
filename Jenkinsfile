pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                echo '🎉 Étape 1: Préparation de l environnement'
                sh 'echo "Bonjour Jenkins!"'
            }
        }
        
        stage('Build') {
            steps {
                echo '🔨 Étape 2: Construction'
                sh '''
                    echo "Construction en cours..."
                    ls -la
                    pwd
                '''
            }
        }
        
        stage('Test') {
            steps {
                echo '🧪 Étape 3: Tests'
                sh 'echo "Exécution des tests..."'
            }
        }
        
        stage('Deploy') {
            steps {
                echo '🚀 Étape 4: Déploiement'
                sh 'echo "Déploiement réussi!"'
            }
        }
    }
    
    post {
        always {
            echo '✅ Pipeline terminé!'
        }
    }
}
