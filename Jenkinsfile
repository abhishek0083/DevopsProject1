pipeline {
    agent any
    environment {
        DOCKER_IMAGE = 'abhishek0083/my-website'
        K8S_NAMESPACE = 'ecommerce'
        KUBECONFIG = 'C:\\jenkins-kube\\config' 
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
                        bat """
                        echo Logging into Docker Hub...
                        docker login -u %DOCKER_USER% -p %DOCKER_PASSWORD%
                        """
                    }

                    bat "docker push ${env.DOCKER_IMAGE}:latest"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    bat """
                    SET KUBECONFIG=${env.KUBECONFIG}
                    kubectl config use-context minikube
                    kubectl get namespace ${env.K8S_NAMESPACE} || kubectl create namespace ${env.K8S_NAMESPACE}
                    kubectl apply -f k8s/ -n ${env.K8S_NAMESPACE}
                    kubectl get pods -n ${env.K8S_NAMESPACE}
                    kubectl get svc -n ${env.K8S_NAMESPACE}
                    """
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
