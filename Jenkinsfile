pipeline {
    agent any
    tools {
        nodejs 'nodejs-20.11.0' 
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
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm install
                '''
            }
        }
 
        stage('Lint') {
            steps {
                // Run linting to ensure code quality
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm run lint
                '''
            }
        }
 
        stage('Build') {
            steps {
                // Build the React app
                bat '''
                set PATH=%NODEJS_HOME%;%PATH%
                npm run build
                '''
            }
        }
 
        stage('SonarQube Analysis') {
            environment {
                SONAR_TOKEN = 'sqp_dde98195ae7ce6f90f35f81dc69fdff2d148de55' // New SonarQube token
            }
            steps {
                // Ensure that sonar-scanner is in the PATH
                sh '''
                export PATH=$SONAR_SCANNER_PATH:$PATH
                which sonar-scanner || echo "SonarQube scanner not found. Please install it."
                sonar-scanner -Dsonar.projectKey=new1 \
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
