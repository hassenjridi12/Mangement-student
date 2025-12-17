/**
 * Pipeline Déclarative Jenkins pour le projet 'student-management'.
 *
 * Utilise les outils spécifiés, le token de sécurité pour SonarQube et intègre
 * la vérification de la Quality Gate pour garantir la qualité du code avant l'archivage.
 */
pipeline {
    agent any

    // Déclaration des outils configurés dans Jenkins
    tools {
        // Le nom 'Maven-3.9.11' est conservé.
        maven 'Maven-3.9.11' 
        
        // Utilisation du nom standard 'JAVA_HOME' pour le JDK 17
        jdk 'JAVA_HOME' 
    }

    environment {
        // Le token est sécurisé par Jenkins Credentials Manager
        SONAR_TOKEN = credentials('SONARQUBE_TOKEN')
        // Nom de l'artefact
        ARTIFACT_NAME = 'target/*.jar'
    }

    stages {
        stage('1. Clone Repository') {
            steps {
                echo 'Clonage du repository Git...'
                git branch: 'main', url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }

        stage('2. Build, Test and Package') {
            steps {
                echo 'Compilation, exécution des tests et packaging...'
                // Commande Maven pour Windows
                bat 'mvn clean install'
                echo 'Build, tests et package terminés.'
            }
        }

        stage('3. SonarQube Analysis') {
            steps {
                echo 'Analyse de qualité du code avec SonarQube...'
                withSonarQubeEnv('sonarqube') {
                    // Commande SonarQube pour Windows (avec '^' pour le saut de ligne et %VAR% pour les variables)
                    bat """
                    mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar ^
                        -Dsonar.projectKey=student-management ^
                        -Dsonar.host.url=http://localhost:9000 ^
                        -Dsonar.login=%SONAR_TOKEN%
                    """
                }
                echo 'Analyse SonarQube soumise au serveur.'
            }
        }

        stage('4. Quality Gate Check (CRITICAL)') {
            steps {
                script {
                    echo 'Vérification du statut de la Quality Gate SonarQube...'
                    // Attend que l'analyse SonarQube soit traitée
                    timeout(time: 10, unit: 'MINUTES') { 
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "La Quality Gate SonarQube a ÉCHOUÉ (Statut: ${qg.status}). La pipeline est arrêtée."
                        }
                        echo 'La Quality Gate a réussi. Le code est conforme aux standards.'
                    }
                }
            }
        }

        stage('5. Archive Artifact') {
            steps {
                echo 'Archivage du fichier JAR...'
                archiveArtifacts artifacts: ARTIFACT_NAME, fingerprint: true
            }
        }
    }

    post {
        failure {
            echo "--- Échec du Pipeline ---"
            echo "Vérifiez l'étape qui a échoué (Build/Test ou Quality Gate)."
        }
        success {
            echo "--- Pipeline terminé avec succès ---"
            echo "L'artefact est prêt pour le déploiement."
        }
    }
}
