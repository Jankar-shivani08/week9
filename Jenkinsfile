pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'shivani0824/week8:latest'
        KUBE_CONFIG  = 'C:\\ProgramData\\Jenkins\\.jenkins\\.kube\\config'
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/Jankar-shivani08/week7-jenkins-docker.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %DOCKER_IMAGE% .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-credentials',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    // CHANGED THIS LINE TO FIX WINDOWS ECHO ERROR
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_IMAGE%'
            }
        }

        stage('Check Kubernetes') {
            steps {
                bat 'kubectl --kubeconfig="%KUBE_CONFIG%" config current-context'
                bat 'kubectl --kubeconfig="%KUBE_CONFIG%" get nodes'
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat 'kubectl --kubeconfig="%KUBE_CONFIG%" apply -f deployment.yaml'
                bat 'kubectl --kubeconfig="%KUBE_CONFIG%" apply -f service.yaml'
            }
        }

        stage('Verify Deployment') {
            steps {
                bat 'kubectl --kubeconfig="%KUBE_CONFIG%" get deployments'
                bat 'kubectl --kubeconfig="%KUBE_CONFIG%" get pods'
                bat 'kubectl --kubeconfig="%KUBE_CONFIG%" get services'
            }
        }
    }

    post {
        success {
            echo 'Week 9 pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check the first error in Console Output.'
        }
    }
}
