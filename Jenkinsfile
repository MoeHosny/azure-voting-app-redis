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
kubectl set image deployment/azure-vote-front azure-vote-front=$WEB_IMAGE_NAME --kubeconfig /var/lib/jenkins/config  '''
      }
    }

    stage('Create Kafka') {
      environment {
        message_retention = '4'
        partition_count = '2'
      }
      steps {
        sh '''
#kafka powershell 
#--------------------
# login to azure
az login --service-principal --username 61df7fd5-cc86-4f12-95ab-6aab8cb8d214 --password f3cafe20-0f95-4fc8-adcb-8c914fce5a66 --tenant a991fd11-f309-430f-ae64-974e3a30ca92

# create resource group
az group create  -n  ${ResourceGroupName}${BUILD_NUMBER} -l "UAE North" -g ${ResourceGroupName}${BUILD_NUMBER}


#Create Event hub Namespace
az eventhubs namespace create --name "EventHubNameSpace"${ResourceGroupName}${BUILD_NUMBER}  --resource-group  ${ResourceGroupName}${BUILD_NUMBER}  -l "UAE North"

#create Event hub
az eventhubs eventhub create --name "Topic"${ResourceGroupName}${BUILD_NUMBER}  --resource-group ${ResourceGroupName}${BUILD_NUMBER}  --namespace-name "EventHubNameSpace"${ResourceGroupName}${BUILD_NUMBER}  --message-retention ${message_retention} --partition-count ${partition_count}

'''
      }
    }

  }
  environment {
    ACR_LOGINSERVER = 'avayaacr.azurecr.io'
    ACR_ID = '328f1fb7-32ad-4b17-ae6f-5d553a9ae9c6'
    ACR_PASSWORD = '6b6d2aa3-5da0-42b4-bc31-c6c2ce6ba945'
    ResourceGroupName = 'RG'
  }
}