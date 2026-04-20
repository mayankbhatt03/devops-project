pipeline {
    agent any

    stages {
        stage('Build Docker Image') {
            steps {
                sh 'docker build -t myapp .'
            }
        }
        
        stage('Stop Old Containers') {
            steps {
                sh '''
                docker ps -q | xargs -r docker stop
                docker ps -aq | xargs -r docker rm
                '''
            }
        }

        stage('Run Container') {
            steps {
                sh 'docker run -d -p 8081:80 --name mycontainer myapp'
            }
        }
    }
}
