pipeline {
    agent any

    environment {
        GO_VERSION = "1.22.3"
        GO_DIR = "${WORKSPACE}/go-install/go"
        PATH = "${WORKSPACE}/go-install/go/bin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/AnithaAnnem/golang-based-task.git', branch: 'main'
            }
        }

        stage('Setup Go') {
            steps {
                echo "Installing Go ${GO_VERSION} in workspace"
                sh '''
                    mkdir -p $WORKSPACE/go-install
                    wget -q https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz
                    tar -C $WORKSPACE/go-install -xzf go${GO_VERSION}.linux-amd64.tar.gz
                    export PATH=$WORKSPACE/go-install/go/bin:$PATH
                    go version
                '''
            }
        }

        stage('Unit Testing') {
            steps {
                dir('example/outyet') {
                    echo "Running unit tests"
                    sh 'go test -v ./...'
                }
            }
        }

        stage('Static Code Analysis') {
            steps {
                dir('example/outyet') {
                    echo "Running go vet and golint"
                    sh '''
                        export PATH=$WORKSPACE/go-install/go/bin:$PATH
                        go vet ./...

                        if ! command -v golint &> /dev/null; then
                            echo "Installing golint..."
                            GO111MODULE=on go install golang.org/x/lint/golint@latest
                            export PATH=$PATH:$(go env GOPATH)/bin
                        fi

                        golint ./... || true
                    '''
                }
            }
        }

        
        stage('Build Application') {
            steps {
                dir('example/outyet') {
                    echo "Building the Go application"
                    sh 'go build ./...'
                }
            }
        }
    }

    post {
        always {
            echo "Pipeline finished"
        }
    }
}
