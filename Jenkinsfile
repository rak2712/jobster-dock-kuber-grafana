pipeline {
    agent any
    environment {
        DOCKER_USER = 'rakshith3'
        DOCKER_PASS = credentials('dockerhub-token')
        GIT_URL = 'https://github.com/rak2712/jobster-dockerized.git'
        GIT_BRANCH = 'main'
    }

    stages {
        stage('Checkout Code') {
            steps { sh 'rm -rf jobster-dockerized && git clone $GIT_URL' }
        }

        stage('Build Docker Images') {
            steps {
                dir('jobster-dockerized/client') { sh 'docker build -t $DOCKER_USER/jobsterfrontend:latest1 .' }
                dir('jobster-dockerized') { sh 'docker build -t $DOCKER_USER/jobsterbackend:latest1 .' }
            }
        }

        stage('Push Docker Images') {
            steps {
                sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                sh 'docker push $DOCKER_USER/jobsterfrontend:latest1'
                sh 'docker push $DOCKER_USER/jobsterbackend:latest1'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh 'kubectl apply -f k8s/'
            }
        }
    }
}
