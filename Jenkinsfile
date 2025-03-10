pipeline {
    agent any
    environment {
        DOCKER_HUB_USER = 'jielin1234'
        DOCKER_HUB_PASS = credentials('dockerhub-credentials-id')
        IMAGE_USER_SERVICE = 'jielin1234/user-service'
        IMAGE_ORDER_SERVICE = 'jielin1234/order-service'
        KUBE_CONFIG = '~/.kube/config'  // Path to your kubeconfig file
        KUBE_CONTEXT = 'minikube'  // Your Kubernetes context
    }
    stages {
        stage('Build User-Service Docker Image') {
            steps {
                withEnv(["PATH=/usr/local/bin:/opt/homebrew/bin:$PATH"]) {
                    sh 'docker build -t $IMAGE_USER_SERVICE ./user-service'
                }
            }
        }
        stage('Build Order-Service Docker Image') {
            steps {
                withEnv(["PATH=/usr/local/bin:/opt/homebrew/bin:$PATH"]) {
                    sh 'docker build -t $IMAGE_ORDER_SERVICE ./order-service'
                }
            }
        }
        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials-id', passwordVariable: 'DOCKER_HUB_PASS', usernameVariable: 'DOCKER_HUB_USER')]) {
                    withEnv(["PATH=/usr/local/bin:/opt/homebrew/bin:$PATH"]) {
                        sh 'docker login -u $DOCKER_HUB_USER -p $DOCKER_HUB_PASS'
                        sh 'docker push $IMAGE_USER_SERVICE'
                        sh 'docker push $IMAGE_ORDER_SERVICE'
                    }
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                withEnv(["KUBEVERSION=/usr/local/bin/kubectl", "KUBE_CONFIG=$KUBE_CONFIG", "KUBE_CONTEXT=$KUBE_CONTEXT"]) {
                    script {
                        sh 'kubectl config use-context $KUBE_CONTEXT'
                        // Assuming you have a Kubernetes deployment YAML for user-service and order-service
                        sh 'kubectl apply -f ./user-service/deployment.yaml'
                        sh 'kubectl apply -f ./order-service/deployment.yaml'
                    }
                }
            }
        }
    }
}

