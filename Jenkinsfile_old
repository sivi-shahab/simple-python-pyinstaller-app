// Scripted Pipeline untuk simple-python-pyinstaller-app dengan Docker
node {
    // Repository URL lokal
    def repoUrl = '/var/jenkins_projects/submission-cicd-pipeline/simple-python-pyinstaller-app'
    
    // Tahap checkout dari repository lokal
    stage('Checkout') {
        // Coba gunakan pendekatan yang lebih sederhana untuk repository lokal
        checkout([
            $class: 'GitSCM', 
            branches: [[name: '*/master']], // Coba gunakan master alih-alih main
            doGenerateSubmoduleConfigurations: false, 
            extensions: [], 
            submoduleCfg: [], 
            userRemoteConfigs: [[url: repoUrl]]
        ])
    }
    
    // Tahap Build dengan Docker
    stage('Build with Docker') {
        // Menggunakan Docker image Python untuk menjalankan PyInstaller
        sh '''
        docker run --rm -v "$PWD:/app" -w /app python:3.9 /bin/bash -c "
            pip install -r requirements.txt &&
            pip install pyinstaller &&
            pyinstaller --onefile app.py
        "
        '''
    }
    
    // Tahap Arsip (menyimpan hasil build)
    stage('Archive') {
        // Menyimpan executable yang dihasilkan
        archiveArtifacts artifacts: 'dist/*', fingerprint: true
    }
}
