pipeline {
    agent any

    environment {
        SONARQUBE_SERVER = 'SonarQubeServer' // URL of the SonarQube server
        SONARQUBE_TOKEN = credentials('sonar-token') // SonarQube token stored in Jenkins credentials
        SCANNER_HOME = tool 'SonarQubeScanner' // The SonarQube Scanner CLI tool configured in Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from GitHub repository
                git url: 'https://github.com/nagappansivagami/ecommerce-store-master.git', branch: 'master'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('SonarQubeServer') {
                    // Run SonarQube scanner
                    sh "${SCANNER_HOME}/bin/sonar-scanner \
                        -Dsonar.projectKey=your-project-key \
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
