pipeline {
    agent none

    stages {

        stage('Checkout') {
            agent { label 'test' }
            steps {
                git branch: 'main', url: 'https://github.com/AYAER392/spring-petclinic.git'
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

                stage('SpotBugs') {
                    agent { label 'test' }
                    steps {
                        bat 'mvnw.cmd com.github.spotbugs:spotbugs-maven-plugin:spotbugs'
                    }
                }
            }
        }

        stage('Documentation et Site') {
            agent { label 'docs' }
            steps {
                bat 'mvnw.cmd -DskipTests site || exit 0'
            }
        }

        stage('Packaging') {
            agent { label 'test' }
            steps {
                bat 'mvnw.cmd clean package'
            }
        }
        stage('Déploiement Nexus') {
            agent { label 'test' }
        
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
