pipeline {
  agent any

  environment {
    IMAGE_NAME = "petclinic"
    IMAGE_TAG  = "${BUILD_NUMBER}"
  }

  stages {
pipeline {
  agent any

  environment {
    IMAGE_NAME = "petclinic"
    IMAGE_TAG  = "${BUILD_NUMBER}"
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
  }

  post {
    success {
      echo "✅ Build + Unit Tests + Sonar completed (all inside Docker)"
    }
    failure {
      echo "❌ Build / Tests / Sonar failed"
    }
  }
}
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Docker Build (Unit Tests + Build)') {
      steps {
        sh '''
          echo "Docker build started (no ports exposed)..."
          docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
        '''
      }
    }

    stage('SonarQube Analysis') {
      steps {
        withSonarQubeEnv('sonarqube') {
          sh '''
            mvn sonar:sonar \
              -Dsonar.projectKey=petclinic \
              -Dsonar.projectName=petclinic \
              -Dsonar.sources=src \
              -Dsonar.java.binaries=target
          '''
        }
      }
    }

    stage('Quality Gate') {
      steps {
        timeout(time: 2, unit: 'MINUTES') {
          waitForQualityGate abortPipeline: true
        }
      }
    }
  }

  post {
    success {
      echo "✅ Build + Unit Tests + SonarQube passed (no port conflicts)"
    }
    failure {
      echo "❌ Pipeline failed"
    }
  }
}
