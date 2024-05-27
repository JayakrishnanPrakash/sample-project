pipeline {
    agent any
    stages {
        stage('Setup Node.js') {
            steps {
                sh '''
                    # Install Node.js and npm
                    curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
                    sudo apt-get install -y nodejs
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
