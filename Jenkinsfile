pipeline {
    agent any

    tools {
        jdk 'JDK-17'        // Make sure this matches your Jenkins JDK installation name
    }

    environment {
        MAVEN_OPTS = '-Xmx1024m'
        SPRING_PROFILES_ACTIVE = 'test'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                echo 'Source code checked out successfully'
            }
        }

        stage('Build') {
            steps {
                echo 'Building the Spring Boot application...'
                sh './mvnw clean compile -DskipTests=true'
            }
        }

        stage('Unit Tests') {
            steps {
                echo 'Skipping unit tests...'
                sh './mvnw test -DskipTests=true'
            }
        }

        stage('Integration Tests') {
            steps {
                echo 'Skipping integration tests...'
                sh './mvnw verify -DskipTests=true'
            }
        }

        stage('Code Coverage') {
            steps {
                echo 'Skipping code coverage (tests disabled)...'
            }
        }

        stage('Static Code Analysis') {
            steps {
                echo 'Running static code analysis...'
                // Uncomment if you have SonarQube configured
                // withSonarQubeEnv('SonarQube') {
                //     sh './mvnw sonar:sonar'
                // }

                // Alternative: Use SpotBugs for static analysis
                sh './mvnw spotbugs:check'
            }
            post {
                always {
                    // Publish SpotBugs results (requires SpotBugs plugin)
                    recordIssues enabledForFailure: true,
                               tools: [spotBugs(pattern: 'target/spotbugsXml.xml')]
                }
            }
        }

        stage('Package') {
            steps {
                echo 'Packaging the application...'
                sh './mvnw package -DskipTests=true'
            }
            post {
                success {
                    // Archive the built JAR file
                    archiveArtifacts artifacts: 'target/*.jar',
                                   fingerprint: true
                }
            }
        }

        stage('Docker Build') {
            when {
                branch 'main' // Only build Docker image on main branch
            }
            steps {
                echo 'Building Docker image...'
                script {
                    def image = docker.build("${env.JOB_NAME}:${env.BUILD_NUMBER}")

                    // Tag as latest if on main branch
                    if (env.BRANCH_NAME == 'main') {
                        image.tag('latest')
                    }
                }
            }
        }

        stage('Security Scan') {
            steps {
                echo 'Running security scan...'
                // OWASP Dependency Check
                sh './mvnw org.owasp:dependency-check-maven:check'
            }
            post {
                always {
                    // Publish dependency check results
                    publishHTML([
                        allowMissing: false,
                        alwaysLinkToLastBuild: true,
                        keepAll: true,
                        reportDir: 'target',
                        reportFiles: 'dependency-check-report.html',
                        reportName: 'OWASP Dependency Check Report'
                    ])
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed!'

            // Clean up workspace
            cleanWs()
        }

        success {
            echo 'Pipeline succeeded!'

            // Send success notification (configure as needed)
            // emailext (
            //     subject: "SUCCESS: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
            //     body: "Good news! The build succeeded.",
            //     to: "${env.CHANGE_AUTHOR_EMAIL}"
            // )
        }

        failure {
            echo 'Pipeline failed!'

            // Send failure notification (configure as needed)
            // emailext (
            //     subject: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'",
            //     body: "Bad news! The build failed. Check console output at ${env.BUILD_URL}",
            //     to: "${env.CHANGE_AUTHOR_EMAIL}"
            // )
        }

        unstable {
            echo 'Pipeline is unstable!'
        }

        changed {
            echo 'Pipeline state changed!'
        }
    }
}