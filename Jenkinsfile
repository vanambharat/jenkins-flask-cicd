pipeline {
    agent any

    environment {
        IMAGE_NAME = "jenkins-flask-demo"
        DOCKER_REGISTRY = "yourdockerhubusername"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/yourusername/jenkins-flask-cicd.git'
            }
        }

        stage('Install Dependencies') {
            steps {
                sh 'pip3 install -r flask-app/requirements.txt'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'pytest flask-app/test_app.py'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t  ./flask-app'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-token', variable: 'DOCKER_TOKEN')]) {
                    sh '''
                        echo  | docker login -u  --password-stdin
                        docker tag  /:latest
                        docker push /:latest
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
