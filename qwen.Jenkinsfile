pipeline {
    agent any

    environment {
        PYTHON_VERSION = '3.9'
        VENV_PATH = 'venv'
        BLACK_CHECK_ARGS = '--check --diff --color'
    }

    stages {
        // Stage 1: Checkout source code from GitHub
        stage('Checkout') {
            steps {
                checkout scm
                echo "✅ Checked out repository: ${env.GIT_URL}"
                // Verify expected project files exist
                sh 'ls -la setup.py requirements.txt || echo "⚠️ Required files not found"'
            }
        }

        // Stage 2: Setup Python environment and install dependencies
        stage('Setup') {
            steps {
                script {
                    sh """
                        # Create virtual environment
                        python${PYTHON_VERSION} -m venv ${VENV_PATH}
                        
                        # Activate and upgrade pip
                        . ${VENV_PATH}/bin/activate
                        python -m pip install --upgrade pip
                        
                        # Install project dependencies from requirements.txt
                        echo "📦 Installing dependencies from requirements.txt..."
                        pip install -r requirements.txt
                        
                        # Install Black for code formatting checks (if not in requirements.txt)
                        if ! pip show black &> /dev/null; then
                            echo "🎨 Installing Black for quality checks..."
                            pip install black
                        fi
                        
                        # Install the project in editable mode (for setup.py projects)
                        echo "🔧 Installing project in editable mode..."
                        pip install -e .
                        
                        echo "✅ Setup completed: Virtual environment ready"
                    """
                }
            }
        }

        // Stage 3: Quality checks - Black code formatting (ENABLED) ✅
        stage('Quality') {
            steps {
                script {
                    sh """
                        . ${VENV_PATH}/bin/activate
                        # Run Black in check mode (validates formatting without modifying files)
                        black ${BLACK_CHECK_ARGS} .
                        echo "✅ Black formatting check passed"
                    """
                }
            }
            post {
                failure {
                    echo "❌ Black formatting issues detected!"
                    echo "💡 To fix locally: run 'black .' in your project root and recommit"
                }
            }
        }

        // Stage 4: Build - Validate setup.py and compile Python bytecode
        stage('Build') {
            steps {
                script {
                    sh """
                        . ${VENV_PATH}/bin/activate
                        
                        # Validate setup.py can be parsed
                        python setup.py --version > /dev/null 2>&1 || echo "⚠️ setup.py version check skipped"
                        
                        # Check for syntax errors by compiling all Python files
                        echo "🔍 Validating Python syntax..."
                        python -m compileall -q .
                        
                        # Optional: Build source distribution (if needed for deployment later)
                        # python setup.py sdist
                        
                        echo "✅ Build completed: Project validated successfully"
                    """
                }
            }
        }

        // Stage 5: Test execution - SKIPPED per configuration
        // tests: "false" → Stage disabled (pytest noted for future use)
        /*
        stage('Test') {
            steps {
                script {
                    sh """
                        . ${VENV_PATH}/bin/activate
                        mkdir -p test-reports
                        pytest tests/ \
                            --cov=app \
                            --cov-report=xml \
                            --junitxml=test-reports/junit.xml \
                            -v
                    """
                }
                post {
                    always {
                        junit testResults: 'test-reports/*.xml', allowEmptyResults: true
                        publishHTML target: [
                            allowMissing: true,
                            reportDir: 'htmlcov',
                            reportFiles: 'index.html',
                            reportName: 'Coverage Report'
                        ]
                    }
                }
            }
        }
        */

        // Stage 6: Deploy - SKIPPED per configuration
        // Dockerfile: "false" → No containerization configured
        /*
        stage('Deploy') {
            when {
                branch 'master'
            }
            steps {
                script {
                    . ${VENV_PATH}/bin/activate
                    echo "ℹ️ Deployment disabled - no Dockerfile configured"
                    // Future options:
                    // - pip install dist/*.whl to target server
                    // - python setup.py build + upload to PyPI
                    // - Add Docker stage when Dockerfile is available
                }
            }
        }
        */
    }

    post {
        always {
            // Cleanup workspace to free resources
            sh "rm -rf ${VENV_PATH} __pycache__ **/__pycache__ .pytest_cache build dist *.egg-info"
            echo "🧹 Workspace cleaned"
        }
        success {
            echo "🎉 Pipeline completed successfully!"
        }
        failure {
            echo "❌ Pipeline failed - check console output for details"
            // Optional: Add notification logic here
            // mail to: 'team@example.com', subject: "Build Failed: ${env.JOB_NAME} #${env.BUILD_NUMBER}"
        }
    }
}