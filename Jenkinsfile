pipeline {
    agent any

    environment {
        // NodeJS version cho backend
        NODE_VERSION = '18'
        // Flutter SDK path (trong Jenkins Ubuntu server của bạn)
        FLUTTER_HOME = '/var/lib/jenkins/flutter/bin'
        PATH = "$PATH:$FLUTTER_HOME:/usr/local/bin"
    }

    stages {

        stage('Checkout') {
            steps {
                echo ' Cloning source code...'
                git branch: 'main', url: 'https://github.com/mingnhi/deploy_booking_app.git'
            }
        }

        stage('Backend - Install & Build') {
            steps {
                dir('backend') {
                    echo 'Installing dependencies for NestJS...'
                    sh '''
                        # Kiểm tra NodeJS
                        node -v || curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | sudo -E bash -
                        sudo apt-get install -y nodejs

                        npm install
                        npm run build
                    '''
                }
            }
        }

        stage('Frontend - Install & Build') {
            steps {
                dir('frontend') {
                    echo 'Building Flutter web project...'
                    sh '''
                        # Fix lỗi Git "dubious ownership" (do Flutter SDK là repo Git)
                        git config --global --add safe.directory /var/lib/jenkins/flutter || true

                        # Export lại PATH để Flutter hoạt động
                        export PATH="$PATH:${FLUTTER_HOME}"

                        # Kiểm tra Flutter version
                        flutter --version

                        # Build Flutter web
                        flutter clean
                        flutter pub get
                        flutter build web
                    '''
                }
            }
        }

        stage('Archive Artifacts') {
            steps {
                echo 'Archiving build outputs...'
                archiveArtifacts artifacts: 'backend/dist/**', fingerprint: true
                archiveArtifacts artifacts: 'frontend/build/web/**', fingerprint: true
            }
        }

        stage('Deploy (Optional)') {
            when {
                expression { return false }  // Tạm tắt deploy
            }
            steps {
                echo ' Deploying application...'
            }
        }
    }

    post {
        success {
            echo '✅ Build completed successfully!'
        }
        failure {
            echo ' Build failed! Check logs for details.'
        }
    }
}
