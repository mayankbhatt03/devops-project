pipeline {
  agent any

  environment {
    IMAGE_NAME = "portfolio:latest"
    ECR_REPO = "501233818458.dkr.ecr.ap-south-1.amazonaws.com/portfolio:latest"
    AWS_REGION = "ap-south-1"
  }

  stages {

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME .'
      }
    }

    stage('Login to ECR') {
      steps {
        sh '''
          aws ecr get-login-password --region $AWS_REGION | \
          docker login --username AWS --password-stdin 501233818458.dkr.ecr.ap-south-1.amazonaws.com
        '''
      }
    }

    stage('Tag Image') {
      steps {
        sh 'docker tag $IMAGE_NAME $ECR_REPO'
      }
    }

    stage('Push to ECR') {
      steps {
        sh 'docker push $ECR_REPO'
      }
    }

    stage('Deploy Container') {
      steps {
        sh '''
          docker rm -f portfolio || true
          docker run -d -p 80:80 --name portfolio $IMAGE_NAME
        '''
      }
    }
  }
}
