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

  }

  post {
    success {
      echo "✅ Build + Unit Tests + Sonar completed successfully"
    }
    failure {
      echo "❌ Pipeline failed"
    }
  }
}
