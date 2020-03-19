pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh '''# Build new image and push to ACR.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
docker build -t $WEB_IMAGE_NAME ./azure-vote
docker login ${ACR_LOGINSERVER} -u ${ACR_ID} -p ${ACR_PASSWORD}
docker push $WEB_IMAGE_NAME'''
      }
    }

    stage('push image from ACR to Kubernets') {
      steps {
        sh '''# Update kubernetes deployment with new image.
WEB_IMAGE_NAME="${ACR_LOGINSERVER}/azure-vote-front:kube${BUILD_NUMBER}"
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME '''
      }
    }

  }
  environment {
    ACR_LOGINSERVER = 'avayaacr.azurecr.io'
    ACR_ID = '328f1fb7-32ad-4b17-ae6f-5d553a9ae9c6'
    ACR_PASSWORD = '6b6d2aa3-5da0-42b4-bc31-c6c2ce6ba945'
  }
}