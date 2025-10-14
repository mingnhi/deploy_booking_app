pipeline {
    agent any

    environment {
        // NodeJS version cho backend
        NODE_VERSION = '18'
        // Flutter SDK path nếu đã cài global trong Jenkins agent
        FLUTTER_HOME = '/usr/local/flutter/bin'
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
                    echo '🎨 Building Flutter web project...'
                    sh '''
                        export PATH="$PATH:${FLUTTER_HOME}"
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
                expression { return false } 
            }
            steps {
                echo ' Deploying application...'
            }
        }
    }

    post {
        success {
            echo ' Build completed successfully!'
        }
        failure {
            echo 'Build failed!'
        }
    }
}
