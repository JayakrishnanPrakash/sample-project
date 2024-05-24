pipeline {
    agent any
    environment {
        NVM_DIR = "$HOME/.nvm"  // Directory to install nvm
    }
    stages {
        stage('Setup Node.js') {
            steps {
                // Install nvm and use it to install Node.js and npm
                sh '''
                    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
                    nvm install 16
                    node -v
                    npm -v
                '''
            }
        }
        stage('Build') {
            steps {
                sh '''
                    export NVM_DIR="$HOME/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
                    npm install
                    npm run build
                    ls
                    cd build
                    ls
                '''
            }
        }
        stage('S3 Upload') {  
            steps {
                withAWS(region: 'us-east-1', credentials: '7d321218-b197-4c83-953a-bd157a1825ee') {
                    sh '''
                        export NVM_DIR="$HOME/.nvm"
                        [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
                        ls -la build
                        aws s3 cp build s3://jenkins-react-sample/ --recursive
                    '''
                }
            }
        }
    }
}
