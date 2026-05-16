pipeline {
    agent {
        // Utilise un agent ou un conteneur configuré avec Java 21
        label 'java-21-agent' 
    }
    
    tools {
        // Déclare les outils configurés dans l'interface de Jenkins
        jdk 'JDK-21'
        maven 'Maven-3.9'
    }

    stages {
        stage('Checkout') {
            steps {
                // Récupération du code source depuis le gestionnaire de versions
                checkout scmGit(branches: [[name: 'main']], userRemoteConfigs: [[url: 'https://github.com']])
            }
        }

        stage('Build & Test') {
            steps {
                // Compilation du projet et exécution des tests unitaires
                sh 'mvn clean package'
            }
            post {
                always {
                    // Publication des résultats des tests (ex: JUnit)
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Analyse SonarQube') {
            steps {
                // Analyse statique du code Java 21
                sh 'mvn sonar:sonar'
            }
        }

        stage('Package') {
            steps {
                // Archivage de l'artefact généré (Jar/War)
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}
