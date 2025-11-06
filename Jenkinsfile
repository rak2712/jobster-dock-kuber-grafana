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
            steps { 
                sh 'rm -rf jobster-dockerized && git clone -b $GIT_BRANCH $GIT_URL' 
            }
        }

        stage('Build Docker Images') {
            steps {
                dir('jobster-dockerized/client') { 
                    sh 'docker build -t $DOCKER_USER/jobsterfrontend1 .' 
                }
                dir('jobster-dockerized') { 
                    sh 'docker build -t $DOCKER_USER/jobsterbackend1 .' 
                }
            }
        }

        stage('Push Docker Images') {
            steps {
                sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                sh 'docker push $DOCKER_USER/jobsterfrontend1'
                sh 'docker push $DOCKER_USER/jobsterbackend1'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withCredentials([file(credentialsId: 'kubeconfig-file', variable: 'KUBECONFIG')]) {
                    sh 'kubectl apply -f k8s/ --validate=false'
                }
            }
        }
    }
}
