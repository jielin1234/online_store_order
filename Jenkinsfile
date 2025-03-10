pipeline {
    agent any

    environment {
        DOCKER_HUB_USER = 'jielin1234'  // Your Docker Hub username
        DOCKER_HUB_PASS = credentials('dockerhub-credentials-id') // Jenkins secret for Docker Hub password
        IMAGE_TAG = "latest"            // Tag for the Docker image (e.g., 'latest')
        KUBE_CONTEXT = 'minikube'       // Kubernetes context for Minikube
    }

    stages {
        stage('Checkout') {
            steps {
                // Pull the code from the GitHub repository
                checkout scm
            }
        }

        stage('Build User-Service Docker Image') {
            steps {
                script {
                    // Build Docker image for user-service
                    sh 'docker build -t $DOCKER_HUB_USER/user-service:$IMAGE_TAG -f user-service/Dockerfile .'
                }
            }
        }

        stage('Build Order-Service Docker Image') {
            steps {
                script {
                    // Build Docker image for order-service
                    sh 'docker build -t $DOCKER_HUB_USER/order-service:$IMAGE_TAG -f order-service/Dockerfile .'
                }
            }
        }

        stage('Login to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub using the credentials stored in Jenkins
                    sh 'echo $DOCKER_HUB_PASS | docker login -u $DOCKER_HUB_USER --password-stdin'
                }
            }
        }

        stage('Push User-Service Docker Image') {
            steps {
                script {
                    // Push user-service image to Docker Hub
                    sh 'docker push $DOCKER_HUB_USER/user-service:$IMAGE_TAG'
                }
            }
        }

        stage('Push Order-Service Docker Image') {
            steps {
                script {
                    // Push order-service image to Docker Hub
                    sh 'docker push $DOCKER_HUB_USER/order-service:$IMAGE_TAG'
                }
            }
        }

        stage('Deploy User-Service to Kubernetes') {
            steps {
                script {
                    // Deploy user-service image to Kubernetes (Minikube)
                    sh '''
                    kubectl config use-context $KUBE_CONTEXT
                    kubectl set image deployment/user-service user-service=$DOCKER_HUB_USER/user-service:$IMAGE_TAG
                    '''
                }
            }
        }

        stage('Deploy Order-Service to Kubernetes') {
            steps {
                script {
                    // Deploy order-service image to Kubernetes (Minikube)
                    sh '''
                    kubectl config use-context $KUBE_CONTEXT
                    kubectl set image deployment/order-service order-service=$DOCKER_HUB_USER/order-service:$IMAGE_TAG
                    '''
                }
            }
        }
    }

    post {
        always {
            // Clean up Docker login session
            sh 'docker logout'
        }
    }
}

