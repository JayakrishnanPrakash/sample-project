pipeline {
    agent any
    stages {
        stage('Setup Node.js') {
            steps {
                sh '''
                    # Install nvm (Node Version Manager)
                    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
                    
                    # Load nvm and install Node.js
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm install 16
                    nvm use 16
                    nvm alias default 16
                    node -v
                    npm -v
                '''
            }
        }
        stage('Checkout') { 
            steps {
                git branch: 'sample-project', 
                    url: 'https://github.com/JayakrishnanPrakash/sample-project' 
            }
        }
        stage('Build') {
            steps { 
                sh 'npm install' 
                sh 'npm run build' 
                sh 'ls'
                sh 'cd build'
                sh 'ls' 
            }
        }
        stage('S3 Upload') {
            steps {
                withAWS(region: 'us-east-1', credentials: '7d321218-b197-4c83-953a-bd157a1825ee') {
                    sh 'ls -la build'
                    sh 'aws s3 cp build s3://jenkins-react-sample/ --recursive'
                }
            }
        }
    }
}
