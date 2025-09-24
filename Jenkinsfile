pipeline {
    agent any

    stages {
        stage('Check Java Version') {
            steps {
                sh 'java -version'
                sh 'javac -version'
            }
        }

/*         stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'chmod +x ./mvnw'
                sh './mvnw clean install -DskipTests'
            }
        } */
    }
}