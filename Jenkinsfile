pipeline {
    agent any

    environment {
        RAILWAY_API_TOKEN = credentials('railway-api-token')   // Token Railway (Lưu trong Jenkins Credentials)
        RAILWAY_TOKEN = 'a1fcb830-8a46-4160-81a3-95bfa21636f3'  // ID Project trên Railway
        NPM_CONFIG_PREFIX = "/var/jenkins_home/.npm-global"
        PATH = "/var/jenkins_home/.npm-global/bin:$PATH"
    }

    triggers {
        pollSCM('H/1 * * * *') // Kiểm tra Git mỗi 1 phút
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/tuan0919/my-jenkins.git'
                sh 'ls -R'
            }
        }

        stage('Debug Env') {
            steps {
                sh 'echo "RAILWAY_TOKEN=$RAILWAY_TOKEN"'
                sh 'echo "RAILWAY_PROJECT_ID=$RAILWAY_PROJECT_ID"'
            }
        }

        stage('Check current user') {
            steps {
                sh 'whoami'
            }
        }

        stage('Check Railway CLI') {
            steps {
                sh 'which railway || echo "🚨 Railway CLI not found!"'
                sh 'echo $PATH'
            }
        }


        stage('Setup Railway CLI') {
            steps {
                sh '''
        export NPM_CONFIG_PREFIX=~/.npm-global
        export PATH=$HOME/.npm-global/bin:$PATH

        if ! command -v railway &> /dev/null
        then
            echo "🚀 Cài đặt Railway CLI..."
            npm config set prefix ~/.npm-global
            npm install -g @railway/cli
            echo "✅ Railway CLI đã được cài đặt!"
        else
            echo "✅ Railway CLI đã có sẵn"
        fi

        echo "🔍 Kiểm tra đường dẫn railway: $(which railway)"
                '''
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Project') {
            steps {
                sh 'npm run build'
            }
        }

        stage('Deploy to Railway') {
            steps {
                sh '''
                export RAILWAY_TOKEN=$RAILWAY_TOKEN
                export RAILWAY_API_TOKEN=$RAILWAY_API_TOKEN
                railway up --service=nodejs
                '''
            }
        }
    }

    post {
        success {
            echo '✅ Deployment thành công lên Railway!'
        }
        failure {
            echo '❌ Deployment thất bại, kiểm tra lỗi trong logs.'
        }
    }
}
