pipeline {
    agent any

    environment {
        PYTHON = 'python3'
        PIP    = 'pip3'
    }

    stages {

        stage('Checkout') {
            steps {
                echo 'Checking out source code...'
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                echo 'Installing dependencies...'
                sh '''
                    pip3 install -r requirements.txt
                    pip3 install -e .
                '''
            }
        }

        stage('Quality') {
            steps {
                echo 'Running Black formatter check...'
                sh '''
                    pip3 install black --quiet
                    black --check .
                '''
            }
        }

        stage('Build') {
            steps {
                echo 'Building package...'
                sh '''
                    pip3 install build --quiet
                    python3 -m build
                '''
            }
        }

    }

    post {
        success {
            echo '✅ Pipeline completed successfully.'
        }
        failure {
            echo '❌ Pipeline failed.'
        }
        always {
            cleanWs()
        }
    }
}