pipeline {
    agent any

    tools {
        nodejs 'nodejs-18.17.0' // Updated Node.js tool version name
    }

    environment {
        NODEJS_HOME = '/Users/ariv/.nvm/versions/node/v18.17.0/bin/node' // Path to Node.js
        SONAR_SCANNER_PATH = '/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin' // Path to SonarQube scanner
        PATH = "${NODEJS_HOME}:${SONAR_SCANNER_PATH}:${PATH}" // Update the PATH
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm // Check out the code from the source control
            }
        }

        stage('Install Dependencies') {
            steps {
                sh '''
                export PATH=$NODEJS_HOME:$PATH
                npm install
                '''
            }
        }

        stage('Lint') {
            steps {
                sh '''
                export PATH=$NODEJS_HOME:$PATH
                npm run lint
                '''
            }
        }

        stage('Build') {
            steps {
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
                sh '''
                export PATH=$SONAR_SCANNER_PATH:$PATH
                if ! command -v sonar-scanner &> /dev/null; then
                    echo "SonarQube scanner not found. Please install it."
                    exit 1
                fi
               sonar-scanner -Dsonar.projectKey=pythonproject \
                              -Dsonar.sources=. \
                              -Dsonar.host.url=http://localhost:9000 \
                              -Dsonar.token=sqp_ea47ab56f8ced55a1ec3d3a7fee1f2a9a538c3e4
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
