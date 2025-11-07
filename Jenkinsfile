pipeline {
    agent any

    environment {
        DOCKER_USER      = 'rakshith3'
        DOCKER_PASS      = credentials('dockerhub-token')
        GIT_URL          = 'https://github.com/rak2712/jobster-dockerized.git'
        GIT_BRANCH       = 'main'
        KUBECONFIG_PATH  = '/var/lib/jenkins/kubeconfig.yaml'
    }

    stages {

        stage('Checkout Code') {
            steps { 
                sh '''
                    rm -rf jobster-dockerized
                    git clone -b $GIT_BRANCH $GIT_URL
                '''
            }
        }

        stage('Build Docker Images') {
            steps {
                sh '''
                    cd jobster-dockerized/client
                    docker build -t $DOCKER_USER/jobsterfrontend1 .

                    cd ..
                    docker build -t $DOCKER_USER/jobsterbackend1 .
                '''
            }
        }

        stage('Push Docker Images') {
            steps {
                sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_USER/jobsterfrontend1
                    docker push $DOCKER_USER/jobsterbackend1
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                    export KUBECONFIG=$KUBECONFIG_PATH

                    cd jobster-dockerized/k8s
                    kubectl apply -f .
                '''
            }
        }
    }
}
