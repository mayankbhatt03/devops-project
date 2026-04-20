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

    stage('SonarQube Scan (Optional)') {
      steps {
        script {
          try {
            sh '''
              docker run --rm --network host \
              -v "$PWD:/usr/src" \
              sonarsource/sonar-scanner-cli:latest \
              -Dsonar.host.url=http://localhost:9000 \
              -Dsonar.login=43.205.243.42 \
              -Dsonar.projectKey=devops-portfolio \
              -Dsonar.sources=.
            '''
          } catch (Exception e) {
            echo "Sonar skipped (no crash 👍)"
          }
        }
      }
    }

    stage('Trivy Scan (Optional)') {
      steps {
        sh '''
          docker run --rm \
          -v /var/run/docker.sock:/var/run/docker.sock \
          aquasec/trivy:latest image \
          --severity HIGH,CRITICAL \
          --exit-code 0 \
          $IMAGE_NAME | tee trivy-report.txt
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
