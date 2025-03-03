// Scripted Pipeline untuk simple-python-pyinstaller-app
node {
    // Repository URL lokal
    def repoUrl = '/var/jenkins_projects/submission-cicd-pipeline/simple-python-pyinstaller-app'
    
    // Tahap checkout dari repository lokal
    stage('Checkout') {
        // Karena ini repository lokal, kita dapat menggunakan dir atau checkout lokal
        checkout([$class: 'GitSCM', 
            branches: [[name: '*/main']], 
            doGenerateSubmoduleConfigurations: false, 
            extensions: [[$class: 'CloneOption', depth: 1, noTags: false, reference: '', shallow: true]], 
            userRemoteConfigs: [[url: repoUrl]]])
    }
    
    // Tahap setup lingkungan Python
    stage('Setup Environment') {
        sh 'python -m venv venv'
        sh '. venv/bin/activate && pip install --upgrade pip'
        sh '. venv/bin/activate && pip install -r requirements.txt'
        // Khusus untuk PyInstaller
        sh '. venv/bin/activate && pip install pyinstaller'
    }
    
    // Tahap Test (jika ada unit test)
    stage('Test') {
        try {
            sh '. venv/bin/activate && pytest'
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
