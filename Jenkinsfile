pipeline {
  agent { 
    dockerfile {
      filename 'Dockerfile-curl'
    } 
  }
  stages {
    stage('build') {
      steps {
        sh 'curl wttr.in/malaga'
      }
    }
  }
}