pipeline {
    agent any

    tools {
        nodejs 'nodejs-18.17.0' // Specify the tool version of Node.js
    }

    environment {
        NODEJS_HOME = '/usr/local/bin/node' // Set the path for Node.js
        SONAR_SCANNER_PATH = '/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin' // Set the path for SonarQube scanner
        PATH = "${NODEJS_HOME}:${SONAR_SCANNER_PATH}:${PATH}" // Add both Node.js and Sonar scanner paths to the system PATH
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Check out the source code from the Git repository
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Ensure npm is installed and available
                    sh 'npm install' // Install dependencies using npm
                }
            }
        }

        stage('Lint') {
            steps {
                script {
                    // Run linting (ensure eslint is set up in your project)
                    sh 'npm run lint'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Run build script (ensure "build" is defined in your package.json)
                    sh 'npm run build'
                }
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') // Access SonarQube token from Jenkins credentials
            }
            steps {
                script {
                    // Run SonarQube analysis
                    sh '''
                    if ! command -v sonar-scanner &> /dev/null; then
                        echo "SonarQube scanner not found. Please install it."
                        exit 1
                    fi
                    sonar-scanner -Dsonar.projectKey=react-register-form \
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
