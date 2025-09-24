pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh './mvnw compile'
            }
        }
        stage('Test') {
            steps {
                sh './mvnw test'
            }
        }
        stage('Package') {
            steps {
                sh './mvnw package'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
    }
}
