pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh 'nvm use 22.12.0' 
                    sh 'npm install' 
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
