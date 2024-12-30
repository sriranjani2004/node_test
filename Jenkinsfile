pipeline {
    agent any
    tools {
        nodejs 'nodejs-20' 
    }

    environment {
        NODEJS_HOME = '/usr/local/bin/node'
        SONAR_SCANNER_PATH = '/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Set the PATH and install dependencies using npm
                sh '''
                export PATH=$NODEJS_HOME:$PATH
                npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                // Run linting to ensure code quality
                sh '''
                export PATH=$NODEJS_HOME:$PATH
                npm run lint
                '''
            }
        }

        stage('Build') {
            steps {
                // Build the React app
                sh '''
                export PATH=$NODEJS_HOME:$PATH
                npm run build
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') // Accessing the SonarQube token stored in Jenkins credentials
            }
            steps {
                // Ensure that sonar-scanner is in the PATH
                sh '''
                export PATH=$SONAR_SCANNER_PATH:$PATH
                if ! [ -x "$(command -v sonar-scanner)" ]; then
                  echo "SonarQube scanner not found. Please install it."
                  exit 1
                fi
                sonar-scanner -Dsonar.projectKey=projectnode \
                    -Dsonar.sources=. \
                    -Dsonar.host.url=http://localhost:9000 \
                    -Dsonar.token=$SONAR_TOKEN
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
