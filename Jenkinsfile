pipeline {
    agent any

    stages {
        stage('Prepare') {
            steps {
                sh 'chmod +x ./mvnw'
            }
        }
        stage('Build') {
            steps {
                sh './mvnw clean compile'
            }
        }
        stage('Test') {
            steps {
                sh './mvnw test -DskipTests'  // Skip tests
            }
        }
        stage('Package') {
            steps {
                sh './mvnw package -DskipTests'  // Skip tests during packaging too
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }
    }
}