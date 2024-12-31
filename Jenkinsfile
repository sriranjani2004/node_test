pipeline {
    agent any
    tools {
        nodejs 'nodejs-20' 
    }

    environment {
        // Use double quotes for string assignment
        NODEJS_HOME = "/Users/ariv/.nvm/versions/node/v20.18.1"
        SONAR_SCANNER_PATH = "/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin"
        // Store your SonarQube token securely using Jenkins credentials
        SONAR_TOKEN = credentials('sonar-token')  // Use Jenkins credentials plugin
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Initialize NVM and set the correct Node.js version
                sh '''
                source ~/.nvm/nvm.sh  # Initialize NVM
                export NODEJS_HOME=/Users/ariv/.nvm/versions/node/v20.18.1
                export PATH=$NODEJS_HOME/bin:$PATH
                npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                // Run linting to ensure code quality
                sh '''
                source ~/.nvm/nvm.sh  # Initialize NVM
                export NODEJS_HOME=/Users/ariv/.nvm/versions/node/v20.18.1
                export PATH=$NODEJS_HOME/bin:$PATH
                npm run lint
                '''
            }
        }

        stage('Build') {
            steps {
                // Build the React app
                sh '''
                source ~/.nvm/nvm.sh  # Initialize NVM
                export NODEJS_HOME=/Users/ariv/.nvm/versions/node/v20.18.1
                export PATH=$NODEJS_HOME/bin:$PATH
                npm run build
                '''
            }
        }

        stage('SonarQube Analysis') {
            steps {
                // Ensure that sonar-scanner is in the PATH
                sh '''
                export PATH=$SONAR_SCANNER_PATH:$PATH
                which sonar-scanner || echo "SonarQube scanner not found. Please install it."
                sonar-scanner -Dsonar.projectKey=reactproject \
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
