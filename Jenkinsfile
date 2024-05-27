pipeline {
    agent any
    environment {
        NVM_DIR = "${WORKSPACE}/.nvm" // Define NVM_DIR as an environment variable
        AWS_REGION = 'us-east-1'
        AWS_CREDENTIALS_ID = '7d321218-b197-4c83-953a-bd157a1825ee'
        PATH = "${WORKSPACE}/.local/bin:${env.PATH}" // Add user local bin to PATH
    }
    stages {
        stage('Setup Node.js') {
            steps {
                sh '''
                    # Ensure the NVM_DIR directory exists
                    mkdir -p ${NVM_DIR}
                    
                    # Install nvm (Node Version Manager)
                    curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
                    
                    # Load nvm and install Node.js
                    export NVM_DIR="${NVM_DIR}"
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
                    export NVM_DIR="${NVM_DIR}"
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
        stage('Install AWS CLI') {
            steps {
                sh '''
                    # Check if AWS CLI is installed, if not, install it
                    if ! command -v aws &> /dev/null
                    then
                        echo "AWS CLI not found, installing..."
                        curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
                        unzip -o awscliv2.zip
                        ./aws/install --install-dir ${WORKSPACE}/.local --bin-dir ${WORKSPACE}/.local/bin
                    fi
                '''
            }
        }
        stage('S3 Upload') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: "${AWS_CREDENTIALS_ID}"]]) {
                    sh '''
                        # Load nvm
                        export NVM_DIR="${NVM_DIR}"
                        [ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
                        nvm use 16

                        # Upload to S3
                        ls -la build
                        aws s3 cp build s3://jenkins-react-sample/ --recursive --region ${AWS_REGION}
                    '''
                }
            }
        }
    }
}
