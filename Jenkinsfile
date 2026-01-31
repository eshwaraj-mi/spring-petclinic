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

    stage('Docker Build (with tests)') {
      steps {
        sh '''
          docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
        '''
      }
    }

  stage('Run Container Validation') {
  steps {
    sh '''
      docker run -d --name petclinic-test ${IMAGE_NAME}:${IMAGE_TAG}
      sleep 15
      docker ps | grep petclinic-test
    '''
  }
}
    }
  }

  post {
    always {
      sh 'docker ps -aq | xargs -r docker rm -f'
    }
    success {
      echo "✅ CI pipeline completed successfully"
    }
    failure {
      echo "❌ CI pipeline failed"
    }
  }
}
