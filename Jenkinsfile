pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                ls -la
                echo "Building the Node.js application"
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }
    }
}
