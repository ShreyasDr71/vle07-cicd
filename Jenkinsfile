pipeline {
  agent any

  stages {
    stage('Build') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub',
            usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'docker build -t $DOCKER_USER/myapp:$BUILD_NUMBER -t $DOCKER_USER/myapp:latest .'
        }
      }
    }

    stage('Push') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub',
            usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin'
          sh 'docker push $DOCKER_USER/myapp:$BUILD_NUMBER'
          sh 'docker push $DOCKER_USER/myapp:latest'
        }
      }
    }

    stage('Deploy') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub',
            usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh 'sed "s|IMAGE_PLACEHOLDER|$DOCKER_USER/myapp:$BUILD_NUMBER|" deployment.yaml | kubectl apply -f -'
        }
        sh 'kubectl rollout status deployment/myapp --timeout=120s'
        sh 'kubectl get pods -l app=myapp'
      }
    }
  }

  post {
    always {
      sh 'docker logout || true'
    }
  }
}
