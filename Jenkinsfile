pipeline {
  agent any

  environment {
    IMAGE_NAME = "petclinic"
    IMAGE_TAG  = "${BUILD_NUMBER}"
    SONAR_HOST = "http://http://13.127.182.72:9000"
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
          echo "Running Trivy vulnerability scan..."
          docker run --rm \
            -v /var/run/docker.sock:/var/run/docker.sock \
            aquasec/trivy:latest image \
            --exit-code 1 \
            --severity HIGH,CRITICAL \
            ${IMAGE_NAME}:${IMAGE_TAG}
        '''
      }
    }
  }

  post {
    success {
      echo "✅ Build, Sonar & Trivy security scan passed"
    }
    failure {
      echo "❌ Pipeline failed (build / quality / security)"
    }
  }
}
