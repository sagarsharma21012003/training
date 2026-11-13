pipeline {
  agent any
  environment {
    IMAGE = 'sagarsharma2118/python-docker-app'
  }
  stages {
    stage('Build and push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
          usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker build -t "$IMAGE:$BUILD_NUMBER" .
            docker push "$IMAGE:$BUILD_NUMBER"
            docker logout
          '''
        }
      }
    }
    stage('Deploy to Kubernetes') {
      steps {
        withCredentials([file(credentialsId: 'kubeconfig', variable: 'KUBECONFIG_FILE')]) {
          sh '''
            export KUBECONFIG="$KUBECONFIG_FILE"
            kubectl apply -f k8s/deployment.yaml
            kubectl set image deployment/python-docker-app app="$IMAGE:$BUILD_NUMBER"
            kubectl rollout status deployment/python-docker-app
          '''
        }
      }
    }
  }
}
