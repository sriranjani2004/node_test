pipeline {
    agent any

    tools {
        nodejs 'nodejs-18.17.0' // Ensure this matches the configured Node.js tool in Jenkins
    }

    environment {
        NODEJS_HOME = '/Users/ariv/.nvm/versions/node/v18.17.0' // Correct Node.js path
        SONAR_SCANNER_PATH = '/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin' // Path to SonarQube scanner
        PATH = "${NODEJS_HOME}/bin:${SONAR_SCANNER_PATH}:${PATH}" // Update PATH to include Node.js and SonarQube paths
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Check out the code from source control
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                export PATH=$NODEJS_HOME/bin:$PATH
                npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                export PATH=$NODEJS_HOME/bin:$PATH
                npm run lint
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                export PATH=$NODEJS_HOME/bin:$PATH
                npm run build
                '''
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token') // Access SonarQube token from Jenkins credentials
            }
            steps {
                sh '''
                export PATH=$SONAR_SCANNER_PATH:$PATH
                if ! command -v sonar-scanner &> /dev/null; then
                    echo "SonarQube scanner not found. Please install it."
                    exit 1
                fi
                sonar-scanner -Dsonar.projectKey=pythonproject \
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
