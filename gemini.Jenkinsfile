pipeline {
    agent any

    environment {
        VENV = 'venv'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/vaibhavi-08/btp-example-10', branch: 'main'
            }
        }

        stage('Setup') {
            steps {
                script {
                    echo 'Creating virtual environment and installing dependencies...'
                    sh """
                        python3 -m venv ${VENV}
                        . ${VENV}/bin/activate
                        pip install --upgrade pip
                        pip install -r requirements.txt
                        # Install black for the Quality stage
                        pip install black
                    """
                }
            }
        }

        stage('Quality') {
            steps {
                script {
                    echo 'Running Black code formatting check...'
                    // Validating code style per your configuration
                    sh ". ${VENV}/bin/activate && black --check ."
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    echo 'Building package using setup.py...'
                    // Builds a source distribution and a wheel
                    sh ". ${VENV}/bin/activate && python3 setup.py sdist bdist_wheel"
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Tests are disabled in config; pytest is ready if needed
                    echo 'Tests are disabled. Skipping pytest...'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    echo 'Deploying build artifacts...'
                    // Example: Archive the generated wheel files
                    archiveArtifacts artifacts: 'dist/*.whl', fingerprint: true
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline succeeded: Package built and verified.'
        }
        failure {
            echo 'Pipeline failed: Check linting or build logs.'
        }
        always {
            // Clean up the virtual environment to save disk space
            sh "rm -rf ${VENV}"
        }
    }
}