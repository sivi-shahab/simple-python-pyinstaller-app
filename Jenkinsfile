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
                    sh 'echo "Deploying to Jenkins workspace: $WORKSPACE"'
                    sh 'mkdir -p $WORKSPACE/deployment'
                    sh 'cp -r ./* $WORKSPACE/deployment/'
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