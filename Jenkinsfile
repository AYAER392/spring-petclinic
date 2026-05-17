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
                bat 'mvnw.cmd -DskipTests site'
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
                nexusArtifactUploader(
                    artifacts: [[
                        artifactId: 'spring-petclinic',
                        classifier: '',
                        file: 'target/spring-petclinic-4.0.0-SNAPSHOT.jar',
                        type: 'jar'
                    ]],
                    credentialsId: '7cf6516f-468b-4fa1-b1e0-5817fbe4318d',
                    groupId: 'org.springframework.samples',
                    nexusUrl: 'localhost:8081',
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    repository: 'maven-snapshots',
                    version: '4.0.0-SNAPSHOT'
                )
            }
        }
    }

    post {

        always {

            recordIssues tools: [
                checkStyle(pattern: 'target/checkstyle-result.xml'),
                pmdParser(pattern: 'target/pmd.xml'),
                spotBugs(pattern: 'target/spotbugsXml.xml')
            ]

            publishHTML([
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'target/site',
                reportFiles: 'checkstyle.html',
                reportName: 'Checkstyle Report'
            ])

            publishHTML([
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'target/site',
                reportFiles: 'pmd.html',
                reportName: 'PMD Report'
            ])

            publishHTML([
                allowMissing: true,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'target/site',
                reportFiles: 'spotbugs.html',
                reportName: 'SpotBugs Report'
            ])
        }

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
