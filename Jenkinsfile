pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh "mvn clean test package spring-boot:repackage"
                sh "printenv"
            }
        }
    }
}
