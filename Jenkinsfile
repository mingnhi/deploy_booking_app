pipeline {
    agent any

    environment {
        // NodeJS cho backend
        NODE_VERSION = '18'
        // Flutter SDK path (đang cài ở /var/jenkins_home/flutter)
        FLUTTER_HOME = '/var/jenkins_home/flutter/bin'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Cloning source code...'
                git branch: 'main', url: 'https://github.com/mingnhi/deploy_booking_app.git'
            }
        }

        stage('Backend - Install & Build') {
            steps {
                dir('backend') {
                    echo 'Installing dependencies for NestJS...'
                    sh '''
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
                        git config --global --add safe.directory /usr/local/flutter || true
                        git config --global --add safe.directory /var/jenkins_home/flutter || true
                        
                        # Thêm Flutter vào PATH
                        export PATH="$PATH:${FLUTTER_HOME}"
                        
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
                echo 'Deploying application...'
            }
        }
    }

    post {
        success {
            echo 'Build completed successfully!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
