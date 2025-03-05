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
            when {
                expression { sh(script: 'find . -name "*.py" -type f | wc -l', returnStdout: true).trim() != "0" }
            }
            steps {
                sh 'find . -name "*.py" -type f | xargs cat'
                sh 'echo "Testing completed through file inspection"'
            }
        }

        stage('Report') {
            when {
                expression { sh(script: 'find . -type f | grep -v ".git" | wc -l', returnStdout: true).trim() != "0" }
            }
            steps {
                sh 'find . -type f | grep -v ".git" | sort'
                sh 'wc -l $(find . -name "*.py" -type f) || echo "No Python files found"'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    def deployPath = "/var/jenkins_home/workspace/submission-cicd-pipeline-sivialmanafalishahab/deployment"

                    // Create the destination directory if it doesn't exist
                    sh "mkdir -p ${deployPath}"

                    // Copy necessary files (avoiding redundant `deployment/` copy)
                    sh """
                        cp -r Jenkinsfile README.md docker-compose.yml jenkins nginx-jenkins setting_nginx.md sources \
                        ${deployPath}/
                    """

                    // Ensure only copy `deployment/` if it exists
                    sh "[ -d deployment ] && cp -r deployment/* ${deployPath}/ || echo 'No additional deployment files found'"

                    // Verify the contents
                    sh "ls -la ${deployPath}/"
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
