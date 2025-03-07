pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    // Example: Build your Docker image
                    sh 'docker build -t my-image .'
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Example: Apply Kubernetes deployment
                    sh 'kubectl apply -f deployment.yaml'
                }
            }
        }
    }
}
