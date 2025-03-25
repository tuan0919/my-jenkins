pipeline {
    agent any

    environment {
        RAILWAY_TOKEN = credentials('railway-api-token')   // Token Railway (Lưu trong Jenkins Credentials)
        RAILWAY_PROJECT_ID = '9fc70ae7-0d1d-4080-969a-7ac6aecd4086'  // ID Project trên Railway
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
                export RAILWAY_PROJECT_ID=$RAILWAY_PROJECT_ID
                railway up --service=web
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
