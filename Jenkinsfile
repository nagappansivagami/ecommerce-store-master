pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'http://192.168.101.41:9000/'
        SONARQUBE_TOKEN = credentials('sonarqube-token') // Use Jenkins credentials binding
        SCANNER_HOME = tool 'SonarQubeScanner' // The SonarQube Scanner CLI tool configured in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub repository
                git url: 'https://github.com/nagappansivagami/ecommerce-store-master.git', branch: 'main'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    // Run SonarQube scanner
                    sh "${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=ci-cd-pipline \
                        -Dsonar.sources=. \
                        -Dsonar.host.url=$SONARQUBE_SERVER \
                        -Dsonar.login=$SONARQUBE_TOKEN"
                }
            }
        }

        stage('Quality Gate') {
            steps {
                script {
                    // Wait for the SonarQube analysis to be completed and check the Quality Gate status
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
            }
        }
    }

    post {
        always {
            // Clean up workspace
            cleanWs()
        }
        failure {
            // Notify if the build fails
            mail to: 'dev-team@example.com',
                 subject: "Failed Pipeline: ${currentBuild.fullDisplayName}",
                 body: "Something is wrong with ${env.JOB_NAME} #${env.BUILD_NUMBER}:\n${env.BUILD_URL}"
        }
    }
}
