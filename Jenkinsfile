pipeline {
    agent any

    tools {
        nodejs 'nodejs-22.12.0'  // Ensure this matches the NodeJS tool ID configured in Jenkins
    }

    environment {
        SONAR_SCANNER_PATH = '/Users/ariv/Downloads/sonar-scanner-6.2.1.4610-macosx-x64/bin'
        PATH = "${SONAR_SCANNER_PATH}:${PATH}"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Make nvm available and use Node 22
                    sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
                    nvm install 22.12.0
                    nvm use 22.12.0
                    npm install
                    '''
                }
            }
        }

        stage('Lint') {
            steps {
                script {
                    sh 'npm run lint'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    sh 'npm run build'
                }
            }
        }

        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = credentials('sonar-token')
            }
            steps {
                script {
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
