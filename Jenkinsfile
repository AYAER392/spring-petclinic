pipeline {
    agent any

    stages {

        stage('Build') {
            steps {
                bat 'mvnw.cmd clean install'
            }
        }

        stage('Test') {
            steps {
                bat 'mvnw.cmd test'
            }

            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
    }
}
pipeline {
    agent any

    stages {

        stage('Compilation') {
            steps {
                bat 'mvnw.cmd clean compile'
            }
        }

        stage('Tests Unitaires') {
            steps {
                bat 'mvnw.cmd test'
            }

            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Couverture de code') {
            steps {
                bat 'mvnw.cmd jacoco:report'
            }
        }

        stage('Analyse Qualité') {
            parallel {

                stage('Checkstyle') {
                    steps {
                        bat 'mvnw.cmd checkstyle:checkstyle'
                    }
                }

                stage('PMD') {
                    steps {
                        bat 'mvnw.cmd pmd:pmd'
                    }
                }

                stage('SpotBugs') {
                    steps {
                        bat 'mvnw.cmd spotbugs:spotbugs'
                    }
                }
            }
        }

        stage('Documentation et Site') {
            steps {
                bat 'mvnw.cmd site'
            }
        }

        stage('Packaging') {
            steps {
                bat 'mvnw.cmd package'
            }
        }

        stage('Packaging') {
            steps {
                bat 'mvnw.cmd clean package'
            }
        }

        stage('Déploiement Nexus') {
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
