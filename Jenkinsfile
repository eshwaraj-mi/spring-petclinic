pipeline {
  agent any

  environment {
    IMAGE_NAME = "petclinic"
    IMAGE_TAG  = "${BUILD_NUMBER}"
    AWS_REGION = "ap-south-1"
    ECR_REPO   = "123456789012.dkr.ecr.ap-south-1.amazonaws.com/petclinic"
    SONAR_HOST = "http://<BASTION_PRIVATE_IP>:9000"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Docker Build + Unit Tests + Sonar') {
      steps {
        withCredentials([string(credentialsId: 'sonarqube-token', variable: 'SONAR_TOKEN')]) {
          sh '''
            docker build \
              --build-arg SONAR_HOST_URL=${SONAR_HOST} \
              --build-arg SONAR_TOKEN=${SONAR_TOKEN} \
              -t ${IMAGE_NAME}:${IMAGE_TAG} .
          '''
        }
      }
    }

    stage('Trivy Image Scan') {
      steps {
        sh '''
          docker run --rm \
            -v /var/run/docker.sock:/var/run/docker.sock \
            aquasec/trivy:latest image \
            --exit-code 1 \
            --severity HIGH,CRITICAL \
            ${IMAGE_NAME}:${IMAGE_TAG}
        '''
      }
    }

    stage('Push Image to AWS ECR') {
      steps {
        sh '''
          aws ecr get-login-password --region ${AWS_REGION} \
          | docker login --username AWS --password-stdin ${ECR_REPO}

          docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${ECR_REPO}:${IMAGE_TAG}
          docker push ${ECR_REPO}:${IMAGE_TAG}
        '''
      }
    }
  }

  post {
    success {
      echo "✅ PHASE 4 complete: Image pushed to AWS ECR"
    }
    failure {
      echo "❌ Pipeline failed"
    }
  }
}
