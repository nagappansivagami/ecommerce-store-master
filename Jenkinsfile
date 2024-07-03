pipeline {
    agent any

    environment {
        // Define any environment variables here
        GIT_REPO = 'https://github.com/nagappansivagami/ecommerce-store-master.git'
        BRANCH = 'main'
        BUILD_TOOL = 'maven' // Could be 'maven', 'gradle', etc.
        DEPLOY_PATH = '/var/www/html' // Example deploy path
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the code from Git
                git branch: "${BRANCH}", url: "${GIT_REPO}"
            }
        }

        stage('Build') {
            steps {
                script {
                    // Choose the build tool
                    if (env.BUILD_TOOL == 'maven') {
                        sh 'mvn clean install'
                    } else if (env.BUILD_TOOL == 'gradle') {
                        sh './gradlew build'
                    } else {
                        error "Unsupported build tool: ${env.BUILD_TOOL}"
                    }
                }
            }
        }

        stage('Test') {
            steps {
                // Run tests
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                // Deploy the application
                sh """
                cp -r target/* ${DEPLOY_PATH}/
                """
            }
        }
    }

    post {
        always {
            // Clean up workspace after build
            cleanWs()
        }
        success {
            // Notify on success
            echo 'Build and deployment successful!'
        }
        failure {
            // Notify on failure
            echo 'Build or deployment failed.'
        }
    }
}
``
