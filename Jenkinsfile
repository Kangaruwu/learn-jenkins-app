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
        stage('Tests') {
            parallel {
                stage('Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                        echo "Running tests"
                        test -f build/index.html || (echo "Build failed, build/index.html not found" && exit 1)
                        npm test
                        '''
                    }
                }
                stage('E2E Tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 5
                            echo "Running end-to-end tests"
                            npx playwright test --reporter=html
                        '''
                    }
                }
            }
        }
        
    }
    post {
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
