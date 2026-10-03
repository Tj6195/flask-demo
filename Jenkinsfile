pipeline {
    agent any

    environment {
        REGISTRY = "tejaswini6195"
        IMAGE = "flask-demo"
    }

    stages {

        stage('Build Docker Image') {
            steps {
                bat "docker build -t %REGISTRY%/%IMAGE%:%BUILD_NUMBER% ."
            }
        }

        stage('Login and Push Image') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat '''
                        echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                        docker push %REGISTRY%/%IMAGE%:%BUILD_NUMBER%
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                withKubeConfig([credentialsId: 'minikube-kubeconfig']) {
                    bat '''
                        kubectl set image deployment/web-deploy web=%REGISTRY%/%IMAGE%:%BUILD_NUMBER%
                        kubectl rollout status deployment/web-deploy
                    '''
                }
            }
        }
    }
}