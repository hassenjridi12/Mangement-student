/**
 * Pipeline Jenkins simplifiée pour le projet 'student-management'.
 * Seules les étapes de Checkout Git et de gestion Docker sont conservées.
 */

pipeline {
    agent any

    // Outils requis : Java n'est plus nécessaire, mais Maven pourrait l'être pour nettoyer
    // Je retire les outils inutiles pour un pipeline orienté Docker/Git
    // Note : S'assurer que Docker est installé sur l'agent 'any'.

    environment {
        // Supprime SONAR_TOKEN et ARTIFACT_NAME, car ils ne sont plus utilisés.
        DOCKER_IMAGE_NAME = "hassenjridi12/student-management" // Nom de l'image Docker
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials' // ID de vos identifiants Docker Hub dans Jenkins
    }

    stages {

        /* ---------------------------------------------------------- */
        stage('1. Checkout Repository') {
            steps {
                echo "🎉 Étape 1 : Clonage du repository Git"
                // Ce bloc est conservé et est fonctionnel.
                git branch: 'main', 
                    url: 'https://github.com/hassenjridi12/Mangement-student.git',
                    credentialsId: 'github-credentials'
            }
        }
        
        /* ---------------------------------------------------------- */
        // ÉTAPE AJOUTÉE : CONSTRUCTION DE L'IMAGE DOCKER
        stage('2. Build Docker Image') {
            steps {
                echo "🐳 Étape 2 : Construction de l'image Docker..."
                // Utilise le Dockerfile présent à la racine du workspace
                script {
                    // Si un sous-dossier est nécessaire (comme dans les essais précédents), utilisez 'dir' ici :
                    // dir('student-management-code') {
                        sh "docker build -t ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} ."
                    // }
                    echo "✅ Image construite : ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }
        
        /* ---------------------------------------------------------- */
        // ÉTAPE AJOUTÉE : PUSH DE L'IMAGE DOCKER
        stage('3. Push Docker Image') {
            steps {
                echo "⬆️ Étape 3 : Push de l'image vers Docker Hub..."
                // Utilise withCredentials pour masquer le mot de passe Docker Hub
                withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                    script {
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                        sh "docker push ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER}"
                        sh "docker tag ${DOCKER_IMAGE_NAME}:${BUILD_NUMBER} ${DOCKER_IMAGE_NAME}:latest"
                        sh "docker push ${DOCKER_IMAGE_NAME}:latest"
                        sh "docker logout"
                    }
                    echo "✅ Image poussée avec les tags : ${BUILD_NUMBER} et latest"
                }
            }
        }

    }

    /* -------------------------------------------------------------- */
    post {
        always {
            echo "📋 Nettoyage post-build…"
            // Nettoyage de l'espace de travail (optionnel, mais bonne pratique)
            cleanWs()
        }
        
        success {
            echo "🎉 SUCCESS : Pipeline Docker terminé avec succès !"
        }
        
        failure {
            echo "❌ FAILURE : La pipeline a échoué."
            echo "🔍 Vérifiez les logs pour les erreurs Git ou Docker."
        }
        
        aborted {
            echo "🛑 ABORTED : Pipeline annulé manuellement."
        }
    }
}
