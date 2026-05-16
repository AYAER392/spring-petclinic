pipeline {
    agent none

    stages {

        stage('Checkout') {
            agent { label 'master' }
            steps {
                git 'https://github.com/AYAER392/spring-petclinic.git'
            }
        }

        stage('Compilation') {
            agent { label 'master' }
            steps {
                bat 'mvnw.cmd clean compile'
            }
        }

        stage('Tests Unitaires') {
            agent { label 'master' }
            steps {
                bat 'mvnw.cmd test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Couverture de code (Jacoco)') {
            agent { label 'master' }
            steps {
                bat 'mvnw.cmd jacoco:report'
            }
        }

        stage('Analyse Qualité (multi-nœuds)') {

            parallel {

                stage('Checkstyle - Node1') {
                    agent { label 'master' }
                    steps {
                        bat 'mvnw.cmd checkstyle:checkstyle'
                    }
                }

                stage('PMD - Node2') {
                    agent { label 'master' }
                    steps {
                        bat 'mvnw.cmd pmd:pmd'
                    }
                }
            }
        }

        stage('Documentation et Site') {
            agent { label 'master' }
            steps {
                bat 'mvnw.cmd site'
            }
        }

        stage('Packaging') {
            agent { label 'master' }
            steps {
                bat 'mvnw.cmd clean package'
            }
        }

        stage('Déploiement Nexus') {
            agent { label 'master' }
            steps {
                nexusArtifactUploader artifacts: [[
                    artifactId: 'spring-petclinic',
                    classifier: '',
                    file: 'target/spring-petclinic-4.0.0-SNAPSHOT.jar',
                    type: 'jar'
                ]],
                credentialsId: '46dccfce-4b39-4efc-b24c-e1abadccf416',
                groupId: 'org.springframework.samples',
                nexusUrl: 'localhost:8081',
                nexusVersion: 'nexus3',
                protocol: 'http',
                repository: 'maven-snapshots',
                version: '4.0.0-SNAPSHOT'
            }
        }
    }

    post {
        success {
            echo 'Build réussi'
        }

        failure {
            emailext(
                subject: 'Echec Build Jenkins',
                body: 'Le pipeline a échoué',
                to: 'admin@gmail.com'
            )
        }
    }
}
