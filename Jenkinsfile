pipeline {
  environment {
    REGISTRY = credentials('REGISTRY')
    REGISTRY_HOST = '3.8.148.166'
  }
  agent any
  stages {
    stage('Docker Registry Log in') {
      steps {
        sh 'docker login ${REGISTRY_HOST} \
          -u ${REGISTRY_USR} -p ${REGISTRY_PSW}'
      }
    }
  }
}