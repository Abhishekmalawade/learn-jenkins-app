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
                    image 'node:18-alpine'  // ✅ Keep default node user (NO root)
                    reuseNode true
                }
            }
            steps {
                sh '''
                    echo "Installing zip in home directory..."
                    mkdir -p $HOME/.local/bin
                    wget -qO $HOME/.local/bin/zip https://github.com/madler/zip/releases/download/v3.0/zip-3.0-bin-linux-x86_64
                    chmod +x $HOME/.local/bin/zip
                    export PATH="$HOME/.local/bin:$PATH"

                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    zip -r build.zip build
                '''
                archiveArtifacts artifacts: 'build.zip', fingerprint: true
            }
        }
    }
}