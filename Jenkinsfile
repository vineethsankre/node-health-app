pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "vineethsankre/node-health-app:latest"
        DOCKER_CREDENTIALS_ID = "dockerhub-creds-id"
    }

    stages {
        stage('Clone') {
            steps {
                git 'https://github.com/vineethsankre/node-health-app.git'
            }
        }

        stage('Install') {
            steps {
                sh 'npm install'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Scan with Trivy') {
            steps {
                sh 'trivy image $DOCKER_IMAGE || true'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDENTIALS_ID}", passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
        }
    }
}
