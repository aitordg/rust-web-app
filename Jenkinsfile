pipeline {
  environment {
    REGISTRY = credentials('REGISTRY')
    REGISTRY_HOST = '3.8.148.166'
    DOCKER_NETWORK_NAME = 'docker_network'
    DOCKER_IMAGE = 'web'
    DB_IMAGE = 'mysql'
    MYSQL_ROOT_PASSWORD = 'pass'
    MYSQL_DATABASE = 'heroes'
    MYSQL_USER = 'user'
    MYSQL_PASSWORD = 'password'
    SLACK_CHANNEL = 'a-bit-of-everything'
    SLACK_TEAM_DOMAIN = 'devopspipelines'
  }
  agent any
  stages {
    stage('Docker Registry Log in') {
        steps {
            sh 'docker login ${REGISTRY_HOST} \
            -u ${REGISTRY_USR} -p ${REGISTRY_PSW}'
        }
    }
    //stage('Docker build') {
    //  steps {
    //    sh 'docker build -t ${DOCKER_IMAGE} -f Dockerfile-UnitTest-add .'
    //  }
    //}
    stage('Docker Up') {
        steps {
            sh 'docker network create --driver=bridge \
                --subnet=172.100.1.0/24 --gateway=172.100.1.1 \
                --ip-range=172.100.1.2/25 ${DOCKER_NETWORK_NAME}'
            sh 'docker run --rm -d --name ${DB_IMAGE} \
                -e MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} \
                -e MYSQL_DATABASE=${MYSQL_DATABASE} \
                -e MYSQL_USER=${MYSQL_USER} \
                -e MYSQL_PASSWORD=${MYSQL_PASSWORD} \
                --net ${DOCKER_NETWORK_NAME} ${DB_IMAGE}'
            sh 'docker run --rm -d --name ${DOCKER_IMAGE} \
                -e DATABASE_URL=mysql://${MYSQL_USER}:${MYSQL_PASSWORD}@${DB_IMAGE}:3306/${MYSQL_DATABASE} \
                -e ROCKET_ENV=prod \
                --net ${DOCKER_NETWORK_NAME} ${DOCKER_IMAGE}'
            sh 'sleep 30'
        }
    }
    stage('Smoke Test') {
      steps {
        sh 'docker run --rm --net ${DOCKER_NETWORK_NAME} \
        byrnedo/alpine-curl --fail -I http://${DOCKER_IMAGE}/health'
      }
    }
    stage('DB Migration') {
        agent {
            dockerfile {
                filename 'diesel-cli.dockerfile' 
                args '-v ${PWD}:/volume \
                    -w /volume \
                    --entrypoint="" \
                    --net ${DOCKER_NETWORK_NAME} \
                    -e DATABASE_URL=mysql://${MYSQL_USER}:${MYSQL_PASSWORD}@${DB_IMAGE}:3306/${MYSQL_DATABASE}'
                }
            }
        steps {
            sh 'diesel migration run' 
        }
    }
    stage('Integration Test') {
        agent {
            dockerfile {
                filename 'dockerfiles/python.dockerfile' 
                    args '--net ${DOCKER_NETWORK_NAME} \
                        -e WEB_HOST=${DOCKER_IMAGE} \
                        -e DB_HOST=${DB_IMAGE} \
                        -e DB_DATABASE=${MYSQL_DATABASE} \
                        -e DB_USER=${MYSQL_USER} \
                        -e DB_PASSWORD=${MYSQL_PASSWORD}'
            }
        }
        steps {
            sh 'python3 integration_tests/integration_test.py' 
        }
    }
    stage('Integration Test: E2E') {
        agent {
            dockerfile {
                filename 'dockerfiles/python.dockerfile' 
                args '--net ${DOCKER_NETWORK_NAME} \
                        -e WEB_HOST=${DOCKER_IMAGE}'
            }
        }
        steps {
            sh 'python3 integration_tests/integration_e2e_test.py' 
        }
    }
  }
  post {
    always {
      sh 'docker kill ${DOCKER_IMAGE} ${DB_IMAGE} || true'
      sh 'docker network rm ${DOCKER_NETWORK_NAME} || true'
    }
    success {
        slackSend (
            channel: "${SLACK_CHANNEL}", 
            teamDomain: "${SLACK_TEAM_DOMAIN}", 
            tokenCredentialId: 'SLACK_TOKEN_ID', 
            color: '#00FF00', 
            message: "SUCCESSFUL: Job '${JOB_NAME} [${BUILD_NUMBER}]' (${BUILD_URL})")
    }
    failure {
        slackSend (
            channel: "${SLACK_CHANNEL}", 
            teamDomain: "${SLACK_TEAM_DOMAIN}", 
            tokenCredentialId: 'SLACK_TOKEN_ID', 
            color: '#FF0000', 
            message: "FAILED: Job '${JOB_NAME} [${BUILD_NUMBER}]' (${BUILD_URL})")
    }
  }
}