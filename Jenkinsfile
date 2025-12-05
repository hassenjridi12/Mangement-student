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
        
        // RE-INTÉGRATION DU JDK 17: 
        // ATTENTION: Vous DEVEZ avoir une installation JDK nommée EXACTEMENT 'JDK17' 
        // dans 'Gérer Jenkins > Outils globaux', sinon la pipeline échouera à nouveau.
        jdk 'JDK17' 
    }

    environment {
        // Le token est sécurisé par Jenkins Credentials Manager
        SONAR_TOKEN = credentials('SONARQUBE_TOKEN')
        // Définir ici d'autres variables, par exemple le nom de l'artefact
        ARTIFACT_NAME = 'target/*.jar'
    }

    stages {
        stage('1. Clone Repository') {
            steps {
                echo 'Clonage du repository Git...'
                // Utilisation de votre URL et branche
                git branch: 'main', url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }

        stage('2. Build, Test and Package') {
            steps {
                echo 'Compilation, exécution des tests et packaging...'
                // CORRECTION WINDOWS: Remplacement de 'sh' par 'bat'
                bat 'mvn clean install'
                echo 'Build, tests et package terminés.'
            }
        }

        stage('3. SonarQube Analysis') {
            steps {
                echo 'Analyse de qualité du code avec SonarQube...'
                // Le nom 'sonarqube' doit correspondre au nom du serveur configuré dans Jenkins
                withSonarQubeEnv('sonarqube') {
                    // CORRECTION WINDOWS: Remplacement de 'sh' par 'bat' et adaptation des variables/sauts de ligne
                    bat """
                    mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar ^
                        -Dsonar.projectKey=student-management ^
                        -Dsonar.host.url=http://localhost:9000 ^
                        -Dsonar.login=%SONAR_TOKEN%
                    """
                    // NOTE: Utilisation de '^' pour le saut de ligne et de '%VAR%' pour les variables d'environnement dans le shell bat.
                }
                echo 'Analyse SonarQube soumise au serveur.'
            }
        }

        stage('4. Quality Gate Check (CRITICAL)') {
            steps {
                script {
                    echo 'Vérification du statut de la Quality Gate SonarQube...'
                    // ATTENTION: Cette étape bloque la pipeline et échoue si la qualité n'est pas "OK".
                    timeout(time: 10, unit: 'MINUTES') { // Donne 10 minutes pour le traitement SonarQube
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
                // Utilisation de la variable d'environnement
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
