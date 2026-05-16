pipeline {
    agent none   // IMPORTANT pour multi-nodes

    stages {

        stage('Compilation') {
            agent { label 'master' }   // ou node principal
            steps {
                bat 'mvnw.cmd clean compile'
            }
        }

        stage('Tests Unitaires') {
            agent { label 'test-node' }
            steps {
                bat 'mvnw.cmd test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }

        stage('Couverture de code (JaCoCo)') {
            agent { label 'coverage-node' }
            steps {
                bat 'mvnw.cmd jacoco:report'
            }
        }

        stage('Analyse Qualité') {
            agent { label 'master' }

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
            }
        }

        stage('Documentation (Site Maven)') {
            agent { label 'doc-node' }
            steps {
                bat 'mvnw.cmd site -DskipTests'
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
            echo 'Build réussi sur pipeline multi-nœuds'
        }

        failure {
            emailext(
                subject: 'Echec Build Jenkins',
                body: 'Le pipeline multi-nœuds a échoué',
                to: 'admin@gmail.com'
            )
        }
    }
}
