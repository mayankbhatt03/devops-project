pipeline {
agent any

```
environment {
    AWS_ACCOUNT_ID = "501233818458"
    REGION = "ap-south-1"
    IMAGE_NAME = "portfolio"
    ECR_REPO = "${AWS_ACCOUNT_ID}.dkr.ecr.${REGION}.amazonaws.com/${IMAGE_NAME}"
}

stages {

    stage('Build Docker Image') {
        steps {
            sh 'docker build -t $IMAGE_NAME .'
        }
    }

    stage('Tag Image') {
        steps {
            sh 'docker tag $IMAGE_NAME:latest $ECR_REPO:latest'
        }
    }

    stage('Login to ECR') {
        steps {
            sh '''
            aws ecr get-login-password --region $REGION | \
            docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$REGION.amazonaws.com
            '''
        }
    }

    stage('Push to ECR') {
        steps {
            sh 'docker push $ECR_REPO:latest'
        }
    }

    stage('Deploy to Kubernetes') {
        steps {
            sh '/usr/local/bin/kubectl rollout restart deployment portfolio-deployment'
        }
    }
}
```

}
