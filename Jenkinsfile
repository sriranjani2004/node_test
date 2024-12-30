pipeline {
    agent any

    tools {
        nodejs 'nodejs-22.12.0'  // Ensure this matches the NodeJS tool ID configured in Jenkins
    }

    environment {
        SONAR_SCANNER_PATH = '/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin'  // Set the path for SonarQube scanner
        PATH = "${SONAR_SCANNER_PATH}:${PATH}"  // Add Sonar scanner to PATH
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm  // Checkout the source code from Git
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Ensure npm is available through the NodeJS tool
                    sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"  # This loads nvm
                    nvm install 22.12.0
                    nvm use 22.12.0
                    npm install  // Install dependencies
                    '''
                }
            }
        }

        stage('Lint') {
            steps {
                script {
                    // Run linting
                    sh 'npm run lint'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Run build script
                    sh 'npm run build'
                }
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token')  // Fetch SonarQube token from Jenkins credentials
            }
            steps {
                script {
                    // Run SonarQube analysis using the token from credentials
                    sh '''
                    if ! command -v sonar-scanner &> /dev/null; then
                        echo "SonarQube scanner not found. Please install it."
                        exit 1
                    fi
                    sonar-scanner -Dsonar.projectKey=pythonproject \
                                   -Dsonar.sources=. \
                                   -Dsonar.host.url=http://localhost:9000 \
                                   -Dsonar.token=${SONAR_TOKEN}
                    '''
                }
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
