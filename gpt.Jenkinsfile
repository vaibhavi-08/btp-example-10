pipeline {
    agent any

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Setup') {
            steps {
                sh '''
                python3 -m venv venv
                . venv/bin/activate

                pip install --upgrade pip
                pip install -r requirements.txt
                pip install .
                '''
            }
        }

        stage('Quality') {
            steps {
                sh '''
                . venv/bin/activate

                black --check .
                '''
            }
        }

        stage('Build') {
            steps {
                sh '''
                . venv/bin/activate

                python setup.py build
                '''
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploy stage placeholder'
            }
        }
    }
}