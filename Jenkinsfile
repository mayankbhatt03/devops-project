pipeline {
    agent any

    options {
        skipDefaultCheckout(true)
    }

    environment {
        AWS_REGION = "ap-south-1"
        ECR_REPO = "501233818458.dkr.ecr.ap-south-1.amazonaws.com/portfolio"
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/mayankbhatt03/devops-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $ECR_REPO:$IMAGE_TAG .'
            }
        }

        stage('Scan Image') {
            steps {
                sh 'trivy image $ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Login to ECR') {
            steps {
                sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO'
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push $ECR_REPO:$IMAGE_TAG'
            }
        }

        stage('Deploy') {
            steps {
                script {
                    try {
                        sh 'kubectl set image deployment/portfolio-deployment portfolio=$ECR_REPO:$IMAGE_TAG'
                        sh 'kubectl rollout status deployment/portfolio-deployment'
                    } catch (Exception e) {
                        sh 'kubectl rollout undo deployment portfolio-deployment'
                        error 'Deployment Failed'
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Build & Deploy Successful'
        }
        failure {
            echo 'Build Failed or Rolled Back'
        }
    }
}
