pipeline {
  agent any

  environment {
    IMAGE_NAME = "portfolio:latest"
  }

  stages {

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t $IMAGE_NAME .'
      }
    }

    stage('Stop Old Container') {
      steps {
        sh '''
          docker rm -f portfolio || true
        '''
      }
    }

    stage('Run Container') {
      steps {
        sh '''
          docker run -d -p 80:80 --name portfolio $IMAGE_NAME
        '''
      }
    }
  }
}
