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
        // CORRECTION MAJ: Le nom 'Maven-3.9.11' est conservé.
        maven 'Maven-3.9.11' 
        
        // CORRECTION: Cette ligne a été commentée car Jenkins ne trouve pas d'installation 'JDK17'.
        // Si la compilation échoue, veuillez remplacer 'JDK17' par le nom exact 
        // de votre installation JDK 17 configurée dans Jenkins (ex: 'Java_17_Home').
        // jdk 'JDK17' 
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
                // J'ai corrigé l'URL pour pointer vers la racine du dépôt Git (.git)
                git branch: 'main', url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }

        stage('2. Build, Test and Package') {
            steps {
                echo 'Compilation, exécution des tests et packaging...'
                // NOTE IMPORTANTE: 'mvn clean install' (ou 'verify') exécute CLEAN, COMPILE, TEST, PACKAGE.
                // Le flag -DskipTests est RETIRÉ pour que les tests s'exécutent.
                // Cela combine efficacement vos étapes 'Build', 'Run Tests' et 'Package JAR'.
                sh 'mvn clean install'
                echo 'Build, tests et package terminés.'
            }
        }

        stage('3. SonarQube Analysis') {
            steps {
                echo 'Analyse de qualité du code avec SonarQube...'
                // Le nom 'sonarqube' doit correspondre au nom du serveur configuré dans Jenkins
                withSonarQubeEnv('sonarqube') {
                    sh """
                    mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                        -Dsonar.projectKey=student-management \
                        -Dsonar.host.url=http://localhost:9000 \
                        -Dsonar.login=${SONAR_TOKEN}
                    """
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
