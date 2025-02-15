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
                    bat "docker build --no-cache -t %DOCKER_IMAGE%:latest ."

                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                        bat """
                        echo Logging into Docker Hub...
                        docker login -u %DOCKER_USER% -p %DOCKER_PASSWORD%
                        """
                    }

                    bat "docker push %DOCKER_IMAGE%:latest"
                }
            }
        }
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    bat """
                    kubectl get namespace %K8S_NAMESPACE% || kubectl create namespace %K8S_NAMESPACE%
                    kubectl apply -f k8s/ -n %K8S_NAMESPACE%
                    kubectl get pods -n %K8S_NAMESPACE%
                    kubectl get svc -n %K8S_NAMESPACE%
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
