pipeline {
    agent none

    stages {

        stage('Checkout') {
            agent { label 'test' }
            steps {
                git 'https://github.com/AYAER392/spring-petclinic.git'
            }
        }

        stage('Compilation') {
            agent { label 'test' }
            steps {
                bat 'mvnw.cmd clean compile'
            }
        }

        stage('Tests Unitaires') {
            agent { label 'test' }
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
            agent { label 'coverage' }
            steps {
                bat 'mvnw.cmd jacoco:report'
            }
        }

        stage('Analyse Qualité (multi-nœuds)') {

            parallel {

                stage('Checkstyle') {
                    agent { label 'test' }
                    steps {
                        bat 'mvnw.cmd checkstyle:checkstyle'
                    }
                }

                stage('PMD') {
                    agent { label 'test' }
                    steps {
                        bat 'mvnw.cmd pmd:pmd'
                    }
                }
            }
        }

        stage('Documentation et Site') {
            agent { label 'docs' }
            steps {
                bat 'mvnw.cmd site'
            }
        }

        stage('Packaging') {
            agent { label 'test' }
            steps {
                bat 'mvnw.cmd clean package'
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
