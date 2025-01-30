pipeline {
    agent any

            environment{
                NETLIFY_SITE_ID = '47a26b36-ead5-420f-ba88-cbf1f44a9426'
            }

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
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
                '''
            }
        }

        stage('Test') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                test -f build/index.html
                npm test
                '''
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '--user root'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Installing zip..."
                    apk add --no-cache zip
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "Deploy to production. Site ID: $NETLIFY_SITE_ID"
                    zip -r build.zip build
                '''
                archiveArtifacts artifacts: 'build.zip', fingerprint: true
            }
        }
    }
}
