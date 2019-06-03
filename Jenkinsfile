pipeline {
  agent { docker { image 'ubuntu:latest' } }
  stages {
    stage('build') {
      steps {
        sh 'curl wttr.in'
      }
    }
  }
}