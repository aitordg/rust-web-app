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
    AWS_STAGING = credentials('AWS')
    AWS_STAGING_DEFAULT_REGION = 'eu-west-2'
    AWS_STAGING_CLUSTER_NAME= 'cluster-of-User2'
    AWS_PROD = credentials('AWS')
    AWS_PROD_DEFAULT_REGION = 'eu-west-2'
    AWS_PROD_CLUSTER_NAME= 'cluster-of-User2'
    DOCKER_PF_WEB = 'web-port-forward-smoke-test'
    DOCKER_PF_DB = 'db-port-forward-test'
    K8S_IT_POD = 'integration-tests'
    DOCKER_PF_DB_PROD = 'db-port-forward-prod'
    LB_DOMAIN_NAME = '5de23233-default-ingress-e8c7-1631423480.eu-west-2.elb.amazonaws.com'
  }
  agent any
  stages {
    stage('Docker Registry Log in') {
        steps {
            sh 'docker login ${REGISTRY_HOST} \
            -u ${REGISTRY_USR} -p ${REGISTRY_PSW}'
        }
    }
    stage('Docker build') {
      steps {
        sh 'docker build -t ${DOCKER_IMAGE} -f Dockerfile-UnitTest-add .'
      }
    }
    //stage('Docker Up') {
    //    steps {
    //        sh 'docker network create --driver=bridge \
    //            --subnet=172.100.1.0/24 --gateway=172.100.1.1 \
    //            --ip-range=172.100.1.2/25 ${DOCKER_NETWORK_NAME}'
    //        sh 'docker run --rm -d --name ${DB_IMAGE} \
    //            -e MYSQL_ROOT_PASSWORD=${MYSQL_ROOT_PASSWORD} \
    //            -e MYSQL_DATABASE=${MYSQL_DATABASE} \
    //            -e MYSQL_USER=${MYSQL_USER} \
    //            -e MYSQL_PASSWORD=${MYSQL_PASSWORD} \
    //            --net ${DOCKER_NETWORK_NAME} ${DB_IMAGE}'
    //        sh 'docker run --rm -d --name ${DOCKER_IMAGE} \
    //            -e DATABASE_URL=mysql://${MYSQL_USER}:${MYSQL_PASSWORD}@${DB_IMAGE}:3306/${MYSQL_DATABASE} \
    //            -e ROCKET_ENV=prod \
    //            --net ${DOCKER_NETWORK_NAME} ${DOCKER_IMAGE}'
    //        sh 'sleep 30'
    //    }
    //}
    //stage('Smoke Test') {
    //  steps {
    //    sh 'docker run --rm --net ${DOCKER_NETWORK_NAME} \
    //    byrnedo/alpine-curl --fail -I http://${DOCKER_IMAGE}/health'
    //  }
    //}
    //stage('DB Migration') {
    //    agent {
    //        dockerfile {
    //            filename 'diesel-cli.dockerfile' 
    //            args '-v ${PWD}:/volume \
    //                -w /volume \
    //                --entrypoint="" \
    //                --net ${DOCKER_NETWORK_NAME} \
    //                -e DATABASE_URL=mysql://${MYSQL_USER}:${MYSQL_PASSWORD}@${DB_IMAGE}:3306/${MYSQL_DATABASE}'
    //            }
    //        }
    //    steps {
    //        sh 'diesel migration run' 
    //    }
    //}
    //stage('Integration Test') {
    //    agent {
    //        dockerfile {
    //            filename 'dockerfiles/python.dockerfile' 
    //                args '--net ${DOCKER_NETWORK_NAME} \
    //                    -e WEB_HOST=${DOCKER_IMAGE} \
    //                    -e DB_HOST=${DB_IMAGE} \
    //                    -e DB_DATABASE=${MYSQL_DATABASE} \
    //                    -e DB_USER=${MYSQL_USER} \
    //                    -e DB_PASSWORD=${MYSQL_PASSWORD}'
    //        }
    //    }
    //    steps {
    //        sh 'python3 integration_tests/integration_test.py' 
    //    }
    //}
    //stage('Integration Test: E2E') {
    //    agent {
    //        dockerfile {
    //            filename 'dockerfiles/python.dockerfile' 
    //            args '--net ${DOCKER_NETWORK_NAME} \
    //                    -e WEB_HOST=${DOCKER_IMAGE}'
    //        }
    //    }
    //    steps {
    //        sh 'python3 integration_tests/integration_e2e_test.py' 
    //    }
    //}
    stage('Docker Push') {
        steps {
            sh 'docker tag \
                ${DOCKER_IMAGE} ${REGISTRY_HOST}/${DOCKER_IMAGE}'
            sh 'docker push ${REGISTRY_HOST}/${DOCKER_IMAGE}'
            sh 'docker tag \
                ${DOCKER_IMAGE} ${REGISTRY_HOST}/${DOCKER_IMAGE}:${BUILD_NUMBER}'
            sh 'docker push ${REGISTRY_HOST}/${DOCKER_IMAGE}:${BUILD_NUMBER}'
        }
    }
    //stage('Connect to K8S Staging') {
    //    steps {
    //        sh 'docker run -v ${HOME}:/root \
    //            -v /var/run/docker.sock:/var/run/docker.sock \
    //            -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
    //            -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW} \
    //            mendrugory/awscli \
    //            aws eks --region ${AWS_STAGING_DEFAULT_REGION} \
    //            update-kubeconfig --name ${AWS_STAGING_CLUSTER_NAME}'
    //    }
    //}
    //stage('Get K8S Pods') {
    //    steps {
    //        sh 'docker run -v ${HOME}:/root \
    //            -v /var/run/docker.sock:/var/run/docker.sock \
    //            -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
    //            -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW} \
    //            mendrugory/ekskubectl \
    //            kubectl get pods'
    //    }
    //}
    //stage('Deploy to Staging') {
    //    agent {
    //        docker {
    //            image 'mendrugory/ekskubectl'
    //            args '-v ${HOME}/.kube:/root/.kube \
    //                -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
    //                -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW}'
    //        }
    //    }                        
    //    steps {
    //        sh 'kubectl apply -f deployment/staging/staging.yml'
    //        sh 'kubectl apply -f deployment/staging/integration_tests.yaml'
    //    }                
    //}
    //stage('Staging: Port forwarding') {     
    //    steps {
    //        script {
    //            PODNAME = sh(script: "docker run \
    //                -v ${HOME}/.kube:/root/.kube \
    //                -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
    //                -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW} \
    //                mendrugory/ekskubectl \
    //                kubectl get pods -n staging -l app=web \
    //                -o jsonpath='{.items[0].metadata.name}'", 
    //                returnStdout: true)
    //            echo "The pod is ${PODNAME}"
    //            sh(script: "docker run \
    //                --name ${DOCKER_PF_WEB} \
    //                -v ${HOME}/.kube:/root/.kube -p 8888:8888 --rm \
    //                -v /var/run/docker.sock:/var/run/docker.sock    \
    //                -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
    //                -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW} \
    //                mendrugory/ekskubectl \
    //                kubectl port-forward \
    //                --address 0.0.0.0 -n staging \
    //                ${PODNAME} 8888:80 &")
    //            sh 'sleep 10'
    //        }
    //    }
    //}
    //stage('Staging: Smoke Testing') {
    //    steps {
    //        sh 'docker run --net=host --rm \
    //            byrnedo/alpine-curl --fail -I http://0.0.0.0:8888/health'
    //    }
    //}
    //stage('Staging: PF DB Migration') {    
    //    steps {
    //        script {
    //            PODNAME = sh(script: "docker run -v ${HOME}/.kube:/root/.kube \
    //                -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
    //                -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW} \
    //                mendrugory/ekskubectl \
    //                kubectl get pods -n staging -l app=db \
    //                -o jsonpath='{.items[0].metadata.name}'", returnStdout: true)
    //            echo "The pod is ${PODNAME}"
    //            sh(script: "docker run --name ${DOCKER_PF_DB} \
    //                -v ${HOME}/.kube:/root/.kube -p 3306:3306 --rm \
    //                -v /var/run/docker.sock:/var/run/docker.sock    \
    //                -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
    //                -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW} \
    //                mendrugory/ekskubectl kubectl port-forward \
    //                --address 0.0.0.0 -n staging ${PODNAME} 3306:3306 &")
    //        }
    //    }
    //}
    //stage('Staging: DB Migration') {
    //    agent {
    //        dockerfile {
    //            filename 'diesel-cli.dockerfile' 
    //                args '--entrypoint="" --net=host \
    //                -e DATABASE_URL=mysql://${MYSQL_USER}:${MYSQL_PASSWORD}@0.0.0.0:3306/${MYSQL_DATABASE}'    
    //        }
    //    }
    //    steps {
    //        sh 'diesel migration run'    
    //    }                
    //}
    //stage('Staging: Integration Test') {
    //    agent {
    //        docker {
    //        image 'mendrugory/ekskubectl'
    //        args '-v ${HOME}/.kube:/root/.kube \
    //        -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
    //        -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW}'
    //        }
    //    }
    //    steps {
    //        sh 'kubectl exec -n staging -it ${K8S_IT_POD} \
    //            -- python3 integration_tests/integration_test.py' 
    //    }                
    //}
    //stage('Staging: Integration Test - E2E') {
    //    agent {
    //        dockerfile {
    //            filename 'dockerfiles/python.dockerfile' 
    //            args '--net=host \
    //                -e WEB_HOST=0.0.0.0:8888'
    //            }
    //        }
    //    steps {
    //        sh 'python3 integration_tests/integration_e2e_test.py' 
    //    }
    //}
    stage('Connect to K8S Production') {
        steps {
            sh 'docker run -v ${HOME}:/root \
                -v /var/run/docker.sock:/var/run/docker.sock \
                -e AWS_ACCESS_KEY_ID=${AWS_PROD_USR} \
                -e AWS_SECRET_ACCESS_KEY=${AWS_PROD_PSW} \
                mendrugory/awscli \
                aws eks --region ${AWS_PROD_DEFAULT_REGION} \
                update-kubeconfig --name ${AWS_PROD_CLUSTER_NAME}'
        }
    }
    stage('Friendly Reminder Authorization before Deploying') {
        steps {
            slackSend (channel: "${SLACK_CHANNEL}", 
                teamDomain: "${SLACK_TEAM_DOMAIN}", 
                tokenCredentialId: 'SLACK_TOKEN_ID', 
                color: '#E8EA25', 
                message: "Job '${JOB_NAME} [${BUILD_NUMBER}]' is waiting for authorization before deploying to production. (${BUILD_URL})")
        }
    }
    /stage('Authorization before Deploying') {
    /    input {
    /        message "Let's Deploy !!!"
    /        ok "Yeaaahh !!!"
    /    } 
    /    steps {
    /        echo "Authorization before Deploying"
    /    } 
    /}
    stage('Deploy Canary to Prodution') {
        agent {
            docker {
                image 'mendrugory/ekskubectl'
                args '-v ${HOME}/.kube:/root/.kube \
                    -e AWS_ACCESS_KEY_ID=${AWS_PROD_USR} \
                    -e AWS_SECRET_ACCESS_KEY=${AWS_PROD_PSW}'
                }
            }
        steps {
            sh "sed 's@{{VERSION}}@$BUILD_NUMBER@g' deployment/prod/canary.yaml.template > deployment/prod/canary.yaml"
            sh 'kubectl apply -f deployment/prod/canary.yaml'
            sh 'kubectl delete -f deployment/prod/proxy.yaml || true'
            sh 'sleep 5'
            sh 'kubectl apply -f deployment/prod/canary-proxy.yaml'
        }
    }
    stage('Canary Testing') {
      steps {
        script{
          sh 'sleep 20;' 
          for (int i = 0; i < 10; i++) {
            echo "Canary Test Request ${i} ..."
              sh 'docker run --net=host --rm byrnedo/alpine-curl --fail http://${LB_DOMAIN_NAME}/health'
              sh 'sleep 1;'
          }
        }                
      }       
    }
    stage('Deploy to Prodution') {
      agent {
        docker {
          image 'mendrugory/ekskubectl'
          args '-v ${HOME}/.kube:/root/.kube \
            -e AWS_ACCESS_KEY_ID=${AWS_PROD_USR} \
            -e AWS_SECRET_ACCESS_KEY=${AWS_PROD_PSW}'
        }
      }            
      steps {
        sh 'kubectl delete -f deployment/prod/canary-proxy.yaml || true'
        sh 'kubectl delete -f deployment/prod/canary.yaml || true'
        sh 'kubectl delete -f deployment/prod/web.yaml || true'
        sh 'sleep 5'
        sh "sed 's@{{VERSION}}@$BUILD_NUMBER@g' deployment/prod/web.yaml.template > deployment/prod/web.yaml"
        sh 'kubectl apply -f deployment/prod/web.yaml'
        sh 'kubectl apply -f deployment/prod/proxy.yaml'
      }        
    }
    stage('Production: Port Forwarding - Tunel') {                     
        steps {
            script {
                PODNAME = sh(script: "docker run -v ${HOME}/.kube:/root/.kube \
                    -e AWS_ACCESS_KEY_ID=${AWS_PROD_USR} \
                    -e AWS_SECRET_ACCESS_KEY=${AWS_PROD_PSW} \
                    mendrugory/ekskubectl kubectl get pods -l app=db \
                    -o jsonpath='{.items[0].metadata.name}'", returnStdout: true)
                echo "The pod is ${PODNAME}"                                        
            sh(script: "docker run --name ${DOCKER_PF_DB_PROD} \
                -v ${HOME}/.kube:/root/.kube -p 3305:3306 --rm \
                -v /var/run/docker.sock:/var/run/docker.sock    \
                -e AWS_ACCESS_KEY_ID=${AWS_PROD_USR} \
                -e AWS_SECRET_ACCESS_KEY=${AWS_PROD_PSW} \
                mendrugory/ekskubectl \
                kubectl port-forward \
                --address 0.0.0.0 ${PODNAME} 3306:3306 &")
            sh 'sleep 10'
            }
        }
    }
    stage('Produntion: DB Migration') {
        agent {
            dockerfile {
                filename 'diesel-cli.dockerfile' 
                args '--entrypoint="" --net=host -e DATABASE_URL=mysql://${MYSQL_USER}:${MYSQL_PASSWORD}@0.0.0.0:3305/${MYSQL_DATABASE}'    
            }
        }
        steps {
            sh 'diesel migration run'
        }
    }
  }
  post {
    //always {
      //sh 'docker kill ${DOCKER_IMAGE} ${DB_IMAGE} ${DOCKER_PF_WEB} ${DOCKER_PF_DB} || true '
      //sh 'docker network rm ${DOCKER_NETWORK_NAME} || true'
      //sh 'docker kill \
      //          web-port-forward-smoke-test || true'
      //sh 'docker run -v ${HOME}/.kube:/root/.kube \
      //      -v /var/run/docker.sock:/var/run/docker.sock \
      //      -e AWS_ACCESS_KEY_ID=${AWS_STAGING_USR} \
      //      -e AWS_SECRET_ACCESS_KEY=${AWS_STAGING_PSW} \
      //      mendrugory/ekskubectl \
      //      kubectl delete po ${K8S_IT_POD} -n staging' 
    //}
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