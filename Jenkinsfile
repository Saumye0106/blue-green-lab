pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t myapp:v2 .'
        sh 'docker save myapp:v2 | docker exec -i minikube ctr -n k8s.io images import -'
      }
    }
    stage('Deploy Green') {
      steps {
        sh 'kubectl apply -f deployment-green.yaml'
        sh 'kubectl rollout restart deployment/myapp-green'
        sh 'kubectl rollout status deployment/myapp-green --timeout=120s'
      }
    }
    stage('Switch Traffic') {
      steps {
        input id: 'switch', message: 'Switch to Green?'
        sh """
          kubectl patch service myapp-service -p '{"spec":{"selector":{"app":"myapp","color":"green"}}}'
        """
      }
    }
  }
}
