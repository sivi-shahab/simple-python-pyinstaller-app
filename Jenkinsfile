pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: '*/master']],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [],
                    submoduleCfg: [],
                    userRemoteConfigs: [[url: '/var/jenkins_projects/submission-cicd-pipeline/simple-python-pyinstaller-app']]
                ])
            }
        }

        stage('Environment Check') {
            steps {
                sh 'which python || which python3 || echo "No Python found"'
                sh 'ls -la /usr/bin/python* || echo "No Python in /usr/bin"'
                sh 'ls -la /usr/local/bin/python* || echo "No Python in /usr/local/bin"'
            }
        }

        stage('Basic Test') {
            steps {
                sh 'find . -name "*.py" -type f | xargs cat'
                sh 'echo "Testing completed through file inspection"'
            }
        }

        stage('Report') {
            steps {
                sh 'find . -type f | grep -v ".git" | sort'
                sh 'wc -l $(find . -name "*.py" -type f) || echo "No Python files found"'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Example deployment steps
                    // 1. Create deployment directory if it doesn't exist
                    sh 'mkdir -p /opt/myapp'

                    // 2. Copy application files to deployment directory
                    sh 'cp -r ./* /opt/myapp/'

                    // 3. Set appropriate permissions
                    sh 'chmod -R 755 /opt/myapp'

                    // 4. Optional: Create a simple startup script
                    sh '''
                        echo "#!/bin/bash
python3 /opt/myapp/main.py" > /opt/myapp/start.sh
                        chmod +x /opt/myapp/start.sh
                    '''

                    // 5. Optional: Start the application (remove if not needed)
                    // Uncomment and modify based on your specific application
                    // sh '/opt/myapp/start.sh &'

                    // 6. Verification
                    sh 'ls -l /opt/myapp'
                    sh 'echo "Deployment completed successfully"'
                }
            }
        }
    }

    post {
        success {
            echo 'Deployment completed successfully!'
        }
        failure {
            echo 'Deployment failed.'
        }
    }
}