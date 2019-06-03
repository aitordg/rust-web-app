pipeline {
  environment {
    REGISTRY = credentials('REGISTRY')
    REGISTRY_HOST = '3.8.148.166'
  }
  agent any
  stages {
    stage('Smoke Test') {
      agent{
          dockerfile{
              filename 'docker-compose.dockerfile'
              args "--net host -v /var/run/docker.sock:/var/run/docker.sock"
          }
      }
      steps {
          sh 'docker-compose up -d'
          sh 'sleep 30'
          sh 'curl --fail -I http://0.0.0.0:8888/health'
      }
      post {
          always {
              sh "docker-compose down"
          }
      }        
    } 
  }
}