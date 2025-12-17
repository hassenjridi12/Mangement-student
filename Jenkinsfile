pipeline {
    agent any

    tools {
        maven 'M2_HOME'      // Nom EXACT de Maven dans Jenkins
        jdk 'JAVA_HOME'      // Nom EXACT du JDK dans Jenkins
    }

    stages {

        stage('1️⃣ Clone Repository') {
            steps {
                echo '📥 Clonage du repository Git...'
                git branch: 'main',
                    url: 'https://github.com/hassenjridi12/Mangement-student.git'
                echo '✅ Clonage terminé'
            }
        }
        

        stage('2️⃣ Build Project') {
            steps {
                echo '🔨 Compilation du projet avec Maven...'
                sh 'mvn clean compile -DskipTests'
                echo '✅ Build terminé'
            }
        }

        stage('3️⃣ Package Project') {
            steps {
                echo '📦 Packaging du projet...'
                sh 'mvn package -DskipTests'
                echo '✅ Packaging terminé'
            }
        }

        // stage('4️⃣ SonarQube Analysis') {
            // steps {
                // echo '🔍 Analyse de la qualité du code avec SonarQube...'
                // withSonarQubeEnv('SonarQube') {
                    // sh """
                    // mvn sonar:sonar \
                    // -Dsonar.projectKey=student-management \
                    // -Dsonar.projectName=student-management
                    // """
                // }
            // }
        // }

        stage('5️⃣ Package JAR') {
            steps {
                echo '📦 Packaging final en JAR...'
                sh 'mvn clean package -DskipTests'
                echo '✅ JAR prêt'
            }
        }

        stage('6️⃣ Archive Artifact') {
            steps {
                echo '📁 Archivage du fichier JAR...'
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
        stage('7️⃣ Build Docker Image') {
    steps {
        echo '🐳 Construction de l’image Docker student-management...'
        sh '''
        docker build -t student-management:1.0 .
        '''
        echo '✅ Image Docker créée avec succès'
    }
}

        stage('Push Docker Image') {
            steps {
                script {
                    // Se connecter à Docker Hub (prends tes identifiants Jenkins)
                    withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', 
                                                     usernameVariable: 'DOCKERHUB_USER', 
                                                     passwordVariable: 'DOCKERHUB_PASS')]) {
                        // Login Docker
                        sh "echo $DOCKERHUB_PASS | docker login -u $DOCKERHUB_USER --password-stdin"
                        
                        // Tag si nécessaire
                        sh "docker tag student-management:1.0 $DOCKERHUB_USER/student-management:1.0"

                        // Push de l'image
                        sh "docker push $DOCKERHUB_USER/student-management:1.0"
                    }
                }
            }
        }
    


    }

    post {
        success {
            echo '🎉 Pipeline terminé avec succès'
        }
        failure {
            echo '❌ Le pipeline a échoué'
        }
    }
}
