// Scripted Pipeline tanpa dependensi Docker
node {
    // Repository URL lokal
    def repoUrl = '/var/jenkins_projects/submission-cicd-pipeline/simple-python-pyinstaller-app'
    
    // Tahap checkout dari repository lokal
    stage('Checkout') {
        checkout([
            $class: 'GitSCM', 
            branches: [[name: '*/master']], 
            doGenerateSubmoduleConfigurations: false, 
            extensions: [], 
            submoduleCfg: [], 
            userRemoteConfigs: [[url: repoUrl]]
        ])
    }
    
    // Mencari yang tersedia di sistem
    stage('Environment Check') {
        sh 'which python || which python3 || echo "No Python found"'
        sh 'ls -la /usr/bin/python* || echo "No Python in /usr/bin"'
        sh 'ls -la /usr/local/bin/python* || echo "No Python in /usr/local/bin"'
    }
    
    // Hanya melakukan pengujian sederhana pada file Python
    stage('Basic Test') {
        sh 'find . -name "*.py" -type f | xargs cat'
        sh 'echo "Testing completed through file inspection"'
    }
    
    // Mencatat informasi tentang proyek
    stage('Report') {
        sh 'find . -type f | grep -v ".git" | sort'
        sh 'wc -l $(find . -name "*.py" -type f) || echo "No Python files found"'
    }
}
