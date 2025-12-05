/**
 * Pipeline Jenkins complet pour le projet 'student-management'.
 * 
 * Fonctionnalités :
 * - Checkout Git
 * - Build + Tests + Packaging avec Maven
 * - Analyse SonarQube
 * - Vérification de la Quality Gate (bloquante)
 * - Archivage du JAR
 * - Post-actions (succès / échec)
 */

pipeline {
    agent any

    tools {
        maven 'Maven-3.9.11'     // Maven configuré dans Jenkins
        jdk 'JAVA_HOME'          // JDK 17 configuré dans Jenkins
    }

    environment {
        SONAR_TOKEN = credentials('SONARQUBE_TOKEN') // Secure Token
        ARTIFACT_NAME = 'target/*.jar'
    }

    stages {

        /* ---------------------------------------------------------- */
        stage('1. Checkout Repository') {
            steps {
                echo "🎉 Étape 1 : Clonage du repository Git"
                git branch: 'main', url: 'https://github.com/hassenjridi12/Mangement-student.git'
            }
        }

        /* ---------------------------------------------------------- */
        stage('2. Clean Workspace') {
            steps {
                echo "🧹 Nettoyage du dossier target…"
                bat "rmdir /s /q target"
            }
        }

        /* ---------------------------------------------------------- */
        stage('3. Build, Test & Package') {
            steps {
                echo "🔨 Compilation + Tests + Packaging…"
                bat "mvn clean install"
            }
        }

        /* ---------------------------------------------------------- */
        stage('4. SonarQube Analysis') {
            steps {
                echo "🔍 Analyse SonarQube en cours…"
                withSonarQubeEnv('sonarqube') {
                    bat """
                        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar ^
                            -Dsonar.projectKey=student-management ^
                            -Dsonar.host.url=http://localhost:9000 ^
                            -Dsonar.login=%SONAR_TOKEN%
                    """
                }
                echo "📡 Analyse SonarQube envoyée."
            }
        }

        /* ---------------------------------------------------------- */
        stage('5. Quality Gate Check (Critical)') {
            steps {
                script {
                    echo "⏳ Vérification de la Quality Gate…"
                    timeout(time: 10, unit: 'MINUTES') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            error "❌ Quality Gate ÉCHOUÉE — Statut : ${qg.status}"
                        }
                    }
                    echo "✅ Quality Gate VALIDÉE — Code conforme."
                }
            }
        }

        /* ---------------------------------------------------------- */
        stage('6. Archive Artifact') {
            steps {
                echo "📦 Archivage de l’artefact JAR…"
                archiveArtifacts artifacts: ARTIFACT_NAME, fingerprint: true
            }
        }
    }

    /* -------------------------------------------------------------- */
    post {
        success {
            echo "🎯 SUCCESS : Pipeline terminé avec succès."
            echo "L’artefact JAR est prêt pour un déploiement."
        }
        failure {
            echo "🚨 FAILURE : Le pipeline a échoué."
            echo "Vérifiez les logs : Build, Tests, SonarQube ou Quality Gate."
        }
        always {
            echo "✔️ Fin du pipeline (post actions exécutées)."
        }
    }
}
