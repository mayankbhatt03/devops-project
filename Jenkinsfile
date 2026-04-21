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

    stage('Trivy Scan') {
  steps {
    sh '''
      docker run --rm \
      -v /var/run/docker.sock:/var/run/docker.sock \
      aquasec/trivy:latest image \
      --severity HIGH,CRITICAL \
      --exit-code 0 \
      $IMAGE_NAME > trivy-report.txt
    '''
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

  post {
    always {
      archiveArtifacts artifacts: 'trivy-report.txt', allowEmptyArchive: true
    }
  }
}
