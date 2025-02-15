pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'abhishek0083/my-website'
        K8S_NAMESPACE = 'ecommerce'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/abhishek0083/DevopsProject1.git'
            }
        }
        stage('Build & Push Docker Image') {
            steps {
                script {
                    bat "docker build --no-cache -t ${env.DOCKER_IMAGE}:latest ."

                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh '''
                        echo "$DOCKER_PASSWORD" | docker login -u "$DOCKER_USER" --password-stdin
                        '''
                    }

                    bat "docker push ${env.DOCKER_IMAGE}:latest"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    bat "kubectl create namespace ${env.K8S_NAMESPACE} --dry-run=client -o yaml | kubectl apply -f -"
                    bat "kubectl apply -f k8s/ -n ${env.K8S_NAMESPACE}"
                    bat "kubectl get pods -n ${env.K8S_NAMESPACE}"
                    bat "kubectl get svc -n ${env.K8S_NAMESPACE}"
                }
            }
        }
    }
    post {
        success {
            echo '🎉 Deployment was successful!'
        }
        failure {
            echo '❌ Deployment failed! Check logs for errors.'
        }
    }
}
