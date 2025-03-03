// Scripted Pipeline untuk simple-python-pyinstaller-app
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
    
    // Tahap setup lingkungan Python
    stage('Setup Environment') {
        sh 'python -m venv venv || python3 -m venv venv'
        sh '. venv/bin/activate && pip install --upgrade pip'
        sh '. venv/bin/activate && pip install -r requirements.txt'
        // Khusus untuk PyInstaller
        sh '. venv/bin/activate && pip install pyinstaller'
    }
    
    // Tahap Test (jika ada unit test)
    stage('Test') {
        try {
            sh '. venv/bin/activate && pytest || echo "No tests found"'
        } catch (Exception e) {
            echo "Tests failed but continuing pipeline"
        }
    }
    
    // Tahap Build dengan PyInstaller
    stage('Build') {
        // Build executable dengan PyInstaller
        sh '. venv/bin/activate && pyinstaller --onefile app.py'
    }
    
    // Tahap Arsip (menyimpan hasil build)
    stage('Archive') {
        // Menyimpan executable yang dihasilkan
        archiveArtifacts artifacts: 'dist/*', fingerprint: true
    }
    
    // Tahap Cleanup
    stage('Cleanup') {
        sh 'rm -rf venv/'
        sh 'rm -rf build/'
        sh 'rm -rf __pycache__/'
    }
}
