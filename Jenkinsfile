pipeline {
    agent any
    tools {
        nodejs 'nodejs-20' // Use the NodeJS tool defined in Jenkins Global Tool Configuration
    }

    environment {
        // Ensure Jenkins uses the installed NodeJS version, not the nvm version
        NODEJS_HOME = '/usr/local/bin/node'
        SONAR_SCANNER_PATH = '/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin'
        // Directly include the Sonar token here (not recommended for security reasons)
        SONAR_TOKEN = 'sqp_13bdfcf460d88304c814d35ac1c76a1adc0b3b67'  // Your Sonar token
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                // Ensure Jenkins uses its configured NodeJS installation
                sh '''
                export PATH=$NODEJS_HOME/bin:$PATH
                npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                // Run linting to ensure code quality
                sh '''
                export PATH=$NODEJS_HOME/bin:$PATH
                npm run lint
                '''
            }
        }

        stage('Build') {
            steps {
                // Build the React app
                sh '''
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
