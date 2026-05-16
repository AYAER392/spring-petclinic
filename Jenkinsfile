pipeline {
    agent any
   
    stages {
        stage ('Build') {
            steps {
            bat 'maven install'
            }
            post {
                success {
                    junit'target/surefire-reports/**/*.xml'
                }
            }
        }
    }
}
