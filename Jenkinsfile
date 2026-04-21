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

    stage('Trivy Scan (No Download Mode)') {
      steps {
        sh '''
          docker run --rm \
          aquasec/trivy:latest image \
          --skip-db-update \
          --skip-java-db-update \
          --severity HIGH,CRITICAL \
          --exit-code 0 \
          $IMAGE_NAME > trivy-report.txt || true
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

    stage('Cleanup') {
      steps {
        sh '''
          docker system prune -f
          rm -rf ~/.cache/trivy
          rm -rf /tmp/*
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
