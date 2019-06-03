pipeline {
  agent any
  stages {
    agent { docker { image 'ubuntu:latest' } }
    stage('build') {
      steps {
        sh 'curl wttr.in'
      }
    }
  }
}