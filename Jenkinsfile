pipeline {
    agent any

    environment {
        IMAGE_NAME = "jenkins-flask-demo"
        DOCKER_REGISTRY = "saibharatvanam34"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/vanambharat/jenkins-flask-cicd.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip3 install --user -r flask-app/requirements.txt'
            }
        }

        stage('Unit Test') {
            steps {
                sh '''
                   export PATH=$HOME/.local/bin:$PATH
                   pytest flask-app/test_app.py
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_REGISTRY/$IMAGE_NAME:latest ./flask-app'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
                    sh '''
                        echo $DOCKER_TOKEN | docker login -u $DOCKER_REGISTRY --password-stdin
                        docker push $DOCKER_REGISTRY/$IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy via Docker Compose') {
            steps {
                sh 'docker-compose down || true'
                sh 'docker-compose up -d --build'
            }
        }
    }

    post {
        success {
            echo '✅ Deployment successful!'
        }
        failure {
            echo '❌ Build failed.'
        }
    }
}

