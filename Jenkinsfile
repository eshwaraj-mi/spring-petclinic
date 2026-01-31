pipeline {
  agent any

  environment {
    IMAGE_NAME = "petclinic"
    IMAGE_TAG  = "${BUILD_NUMBER}"
  }

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Unit Test & Build (Docker)') {
      steps {
        sh '''
          echo "Running unit tests and build inside Docker..."
          docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
        '''
      }
    }
  }

  post {
    always {
      echo "CI run finished"
    }
    success {
      echo "✅ Build & unit tests passed without port conflicts"
    }
    failure {
      echo "❌ Build or unit tests failed"
    }
  }
}
