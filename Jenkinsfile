pipeline {
  agent any

  stages {

    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Unit Tests') {
      steps {
        sh 'mvn test'
      }
    }

    stage('Build Jar') {
      steps {
        sh 'mvn clean package -DskipTests'
      }
    }

    stage('Docker Build') {
      steps {
        sh 'docker build -t petclinic:${BUILD_NUMBER} .'
      }
    }
  }
}
