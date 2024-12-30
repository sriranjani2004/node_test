pipeline {
    agent any
    tools {
        nodejs 'nodejs-20' 
    }

    environment {
        NODEJS_HOME = '/usr/local/bin/node'
        SONAR_SCANNER_PATH = '/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin'
        PATH = "${SONAR_SCANNER_PATH}:${NODEJS_HOME}:${env.PATH}" // Prepend both paths
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Install dependencies using npm
                sh '''
                npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                // Run linting to ensure code quality
                sh '''
                npm run lint
                '''
            }
        }

        stage('Build') {
            steps {
                // Build the React app
                sh '''
                npm run build
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Execute SonarQube analysis
                sh '''
                sonar-scanner -Dsonar.projectKey=projectnode \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.token=sqp_17a33195d4c4d7f3e183f4930f05536b4dbda549
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
        always {
            echo 'This runs regardless of the result.'
        }
    }
}
