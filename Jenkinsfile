pipeline {
    agent any

    environment {
        GO_VERSION = "1.22.3"
        GOROOT = "/usr/local/go"
        PATH = "/usr/local/go/bin:$PATH"
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout your GitHub repo
                git url: 'https://github.com/AnithaAnnem/golang-based-task.git', branch: 'main'
            }
        }

        stage('Setup Go') {
            steps {
                echo "Installing Go ${GO_VERSION}"
                sh '''
                    wget https://go.dev/dl/go${GO_VERSION}.linux-amd64.tar.gz
                    rm -rf /usr/local/go
                    tar -C /usr/local -xzf go${GO_VERSION}.linux-amd64.tar.gz
                    export PATH=$PATH:/usr/local/go/bin
                    go version
                '''
            }
        }

        stage('Run Unit Tests') {
            steps {
                dir('example/outyet') {
                    echo "Running unit tests"
                    sh 'go test -v ./...'
                }
            }
        }

        stage('Code Quality Checks') {
            steps {
                dir('example/outyet') {
                    echo "Running go vet and golint"
                    sh '''
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
                    sh 'go build'
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
