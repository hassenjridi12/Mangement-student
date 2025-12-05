// Ce pipeline est optimisé pour un environnement multi-OS (Linux/Windows), y compris les VM Vagrant.
pipeline {
    // Agent 'any' permet d'exécuter le pipeline sur n'importe quel agent disponible.
    agent any 

    tools {
        // Le nom DOIT correspondre à la configuration globale de l'outil Maven dans Jenkins.
        maven 'Maven-3.9.11' 
    }

    environment {
        // Récupère l'identifiant (Credential ID) du token SonarQube stocké dans Jenkins.
        SONAR_TOKEN = credentials('SONARQUBE_TOKEN')
        // Définit la clé du projet Sonar pour éviter de la répéter dans la commande mvn.
        SONAR_PROJECT_KEY = 'student-management'
    }

    stages {
        stage('1️⃣ Préparation et Clonage') {
            steps {
                echo '📥 Clonage du repository Git...'
                // Utilisation de scm pour cloner la configuration de l'élément (plus robuste)
                checkout scm
                echo '✅ Clonage terminé'
            }
        }

        stage('2️⃣ Compilation du Projet') {
            steps {
                echo '🔨 Compilation du projet avec Maven (sans les tests)...'
                // Compilation avec clean et -DskipTests, pour une compilation propre et rapide.
                script {
                    def command = "mvn clean compile -DskipTests"
                    // Si l'agent est une VM Vagrant (généralement Linux), 'sh' sera utilisé.
                    if (isUnix()) {
                        sh command
                    } else {
                        bat command
                    }
                }
                echo '✅ Compilation terminée'
            }
        }

        stage('3️⃣ Exécution des Tests') {
            steps {
                echo '🧪 Exécution des tests unitaires...'
                // Exécute les tests, l'étape échouera si des tests cassent.
                script {
                    def command = "mvn test"
                    if (isUnix()) {
                        sh command
                    } else {
                        bat command
                    }
                }
                // Publication des résultats de tests JUnit pour affichage dans Jenkins
                junit testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true
                echo '✅ Tests terminés'
            }
        }

        stage('4️⃣ Packaging JAR') {
            steps {
                echo '📦 Packaging du projet en JAR (sans re-exécution des tests)...'
                // Création du JAR final sans re-exécuter les tests.
                script {
                    def command = "mvn package -DskipTests"
                    if (isUnix()) {
                        sh command
                    } else {
                        bat command
                    }
                }
                echo '✅ Package JAR terminé'
            }
        }

        stage('5️⃣ Analyse SonarQube') {
            steps {
                echo '🔍 Analyse de qualité du code avec SonarQube...'
                
                // Le nom 'sonarqube' DOIT correspondre au nom configuré dans Configurer le système.
                withSonarQubeEnv('sonarqube') { 
                    script {
                        // L'analyse doit être faite APRÈS les tests pour inclure la couverture.
                        def sonar_command = "mvn sonar:sonar -Dsonar.projectKey=${env.SONAR_PROJECT_KEY}"
                        
                        if (isUnix()) {
                            sh sonar_command
                        } else {
                            bat sonar_command
                        }
                    }
                }
                echo '✅ Analyse SonarQube lancée'
            }
        }

        stage('6️⃣ Quality Gate Check') {
            steps {
                echo '⏱️ Attente et vérification de la Quality Gate SonarQube...'
                timeout(time: 5, unit: 'MINUTES') {
                    // abortPipeline: true est essentiel pour échouer le job si la qualité n'est pas suffisante
                    waitForQualityGate abortPipeline: true
                }
                echo '✅ Vérification de la Quality Gate terminée (Qualité OK)'
            }
        }

        stage('7️⃣ Archivage de l\'Artefact') {
            // Archive le JAR final produit par 'mvn package'.
            steps {
                echo '📁 Archivage du fichier JAR...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                echo '✅ Archivage terminé'
            }
        }
        
        // STAGE DE DÉPLOIEMENT CONTINU
        stage('8️⃣ Déploiement (Staging)') {
            // Conditionnel : Déploie l'application uniquement si le build provient de la branche 'main'.
            when { 
                branch 'main' 
            }
            steps {
                echo '🚨 Demande de confirmation avant le déploiement sur Staging...'
                
                // Porte manuelle : Le pipeline s'arrête ici en attendant une action humaine.
                timeout(time: 15, unit: 'MINUTES') {
                    input message: 'Approuver le déploiement vers Staging?', ok: 'Déployer'
                }
                
                echo '🚀 Déploiement sur le serveur Staging en cours...'
                
                // Ceci est un placeholder. Dans un environnement Vagrant, vous utiliseriez 
                // souvent 'ssh' pour vous connecter à la VM cible ou un script Ansible/Docker.
                script {
                    def deploy_command = "echo 'Simuler le transfert du JAR vers le serveur de staging et redémarrer le service...'"
                    if (isUnix()) {
                        sh deploy_command
                    } else {
                        bat deploy_command
                    }
                }
                
                echo '✅ Déploiement Staging terminé'
            }
        }
    }

    // Le bloc 'post' est exécuté après toutes les étapes, quel que soit le résultat.
    post {
        failure {
            echo '❌ Le pipeline a échoué. Vérifiez les logs des étapes pour identifier l''erreur.'
        }
        success {
            echo '🎉 Pipeline terminé avec succès. Le JAR est prêt pour le Déploiement Continu.'
        }
    }
}
