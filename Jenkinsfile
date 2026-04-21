pipeline {
  agent any

  environment {
    IMAGE_NAME = "portfolio:latest"
    SONAR_HOST = "http://localhost:9000"   // Sonar running ho to hi
    SONAR_TOKEN = "YOUR_TOKEN"             // optional
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
              -Dsonar.host.url=$SONAR_HOST \
              -Dsonar.login=$SONAR_TOKEN \
              -Dsonar.projectKey=devops-portfolio \
              -Dsonar.sources=.
            '''
          } catch (Exception e) {
            echo "Sonar skipped (no issue 👍)"
          }
        }
      }
    }

    stage('Trivy Scan (Lightweight)') {
      steps {
        sh '''
          docker run --rm \
          -v /var/run/docker.sock:/var/run/docker.sock \
          aquasec/trivy:latest image \
          --scanners vuln \
          --skip-java-db-update \
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
