pipeline {
  agent any

  environment {
    ECR_URI = '614543684417.dkr.ecr.ap-south-1.amazonaws.com/demo-app'
    IMAGE_TAG = "latest"
  }

  stages {
    stage('Checkout') {
      steps {
        // Jenkins already checks out the repo via Pipeline from SCM — this uses it properly
        checkout scm
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t $ECR_URI:$IMAGE_TAG .'
      }
    }

    stage('Push to ECR') {
      steps {
        sh '''
          aws ecr get-login-password --region ap-south-1 | \
          docker login --username AWS --password-stdin $ECR_URI
          docker push $ECR_URI:$IMAGE_TAG
        '''
      }
    }

    stage('Deploy to EKS') {
      steps {
        sh 'kubectl apply -f k8s-manifests/deployment.yaml'
      }
    }
  }
}

