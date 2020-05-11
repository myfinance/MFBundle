pipeline {
 agent none

 environment{
   SERVICE_NAME = "mfbundle"
   ORGANIZATION_NAME = "myfinance"
   DOCKERHUB_USER = "holgerfischer"
   //Snapshot Version
   VERSION = "0.13.0-alpha.${BUILD_ID}"
   //Release Version
   //VERSION = "0.13.0"
   REPOSITORY_TAG = "${DOCKERHUB_USER}/${ORGANIZATION_NAME}-${SERVICE_NAME}:${VERSION}"
   K8N_IP = "192.168.100.73"
   DOCKER_REPO = "${K8N_IP}:31003/repository/mydockerrepo"
   TARGET_HELM_REPO = "http://${K8N_IP}:31001/repository/myhelmrepo/"
 }

 stages{
   stage('preperation'){
    agent {
        docker {
            image 'maven:3.6.3-jdk-8' 
        }
    }      
     steps {
       cleanWs()
       git credentialsId: 'github', url: "https://github.com/${ORGANIZATION_NAME}/${SERVICE_NAME}.git"
     }
   }
   stage('build chart'){
     agent any
     steps {
       sh 'envsubst < ./helm/mfbundle/Chart_template.yaml > ./helm/mfbundle/Chart.yaml'
       sh 'helm dependency update ./helm/mfbundle'
       sh 'helm package helm/mfbundle -u -d helmcharts/'
       sh 'curl ${TARGET_HELM_REPO} --upload-file helmcharts/mfbundle-${VERSION}.tgz -v'
     }
   }
   //stage('cleanup cluster'){
   //  agent any
   //  steps {
   //    //sh 'helm delete mfbackup'
   //    sh 'helm delete mfshell'
   //    sh 'helm delete mffrontend'
   //    sh 'helm delete mfbackend'
   //  }
   //}   
   stage('deploy to cluster'){
     agent any
     steps {
       sh 'helm repo add myrepo ${TARGET_HELM_REPO}'
       sh 'helm repo update'
       sh 'helm repo list'
       sh 'helm search repo mfbundle --devel'
       sh 'helm upgrade -i --cleanup-on-fail mfbundle myrepo/mfbundle --set repository=${DOCKER_REPO}/${DOCKERHUB_USER}/${ORGANIZATION_NAME}- --devel'
     }
   }   
 }
}
