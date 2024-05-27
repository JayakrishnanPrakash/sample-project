pipeline {
    agent any
    environment {
        NVM_DIR = "${WORKSPACE}/.nvm" // Define NVM_DIR as an environment variable
    }
    stages {
        stage('Setup Node.js') {
            steps {
                sh '''
                    # Install nvm (Node Version Manager)
                    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
                    
                    # Load nvm and install Node.js
                    export NVM_DIR="${WORKSPACE}/.nvm"
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
                sh '''
                    # Load nvm
                    export NVM_DIR="${WORKSPACE}/.nvm"
                    [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                    nvm use 16

                    # Run build commands
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
                        # Load nvm
                        export NVM_DIR="${WORKSPACE}/.nvm"
                        [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                        nvm use 16

                        # Upload to S3
                        ls -la build
                        aws s3 cp build s3://jenkins-react-sample/ --recursive
                    '''
                }
            }
        }
    }
}
