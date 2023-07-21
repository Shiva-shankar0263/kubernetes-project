pipeline {
  agent any

  environment {
    DOCKER_REGISTRY="092101872227.dkr.ecr.ap-northeast-2.amazonaws.com"
    K8S_NAMESPACE = 'backend'
    K8S_DEPLOYMENT_NAME = 'kubeproject'
  }

  stages {
    stage('Build Docker Image') {
      steps {
        sh '''
	 whoami
         aws ecr get-login-password --region ap-northeast-2 | docker login --username AWS --password-stdin $DOCKER_REGISTRY
	 docker build -t shivaproject1 .
         docker tag shivaproject1:latest $DOCKER_REGISTRY/shivaproject1:${BUILD_NUMBER}
         docker push $DOCKER_REGISTRY/shivaproject1:${BUILD_NUMBER}
	  '''
      }
    }

    stage('Deploy to Kubernetes') {
      steps {
        sh '''
            sed "s/buildNumber/${BUILD_NUMBER}/g" K8/deployment.yaml > deployment-new.yaml
	    mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$HOME/bin:$PATH
            kubectl apply -f deployment-new.yaml -n $K8S_NAMESPACE
            kubectl apply -f K8/service.yaml -n $K8S_NAMESPACE
           '''
      }
    }
  }
}
