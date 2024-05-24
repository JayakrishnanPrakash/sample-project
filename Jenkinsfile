pipeline {
    agent any
    stages {
        stage('Build') {
            steps { 
                Checkout SCM
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
