pipeline {
  agent { docker { image 'ubuntu:latest' } }
  stages {
    stage('build') {
      steps {
        sh 'apt-get update && apt-get install curl -y'
        sh 'curl wttr.in'
      }
    }
  }
}