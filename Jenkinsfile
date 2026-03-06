pipeline {
  agent any

  environment {
    DOCKERHUB_REPO = "rarebreedxx/ogofi-web"
    K8S_DEPLOY     = "ogofi-web"
    K8S_NS         = "default"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Image') {
      steps {
        sh """
          docker build -t "$rarebreedxx}:${BUILD_NUMBER" .
          docker tag "$rarebreedxx}:${BUILD_NUMBER" "$rarebreedxx:latest"
        """
      }
    }

    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh """
            echo "$DH_PASS" | docker login -u "$DH_USER" --password-stdin
            docker push "$rarebreedxx:$BUILD_NUMBER"
            docker push "$rarebreedxx}:latest"
          """
        }
      }
    }

    stage('Deploy (Apply YAML)') {
      steps {
        sh """
          kubectl apply -f k8s/deployment.yaml -n ${K8S_NS}
          kubectl apply -f k8s/service.yaml -n ${K8S_NS}
        """
      }
    }

    stage('Update Image + Rollout') {
      steps {
        sh """
          kubectl set image deployment/"$K8S_DEPLOY" web="$rarebreedxx}:$BUILD_NUMBER" -n $"K8S_NS"
          kubectl rollout status deployment/"$K8S_DEPLOY" -n "$K8S_NS" --timeout=180s
        """
      }
    }

    stage('Show URL') {
      steps {
        sh """
          set -eux
          kubectl get svc ogofi-web-svc -n "$K8S_NS" -o wide
          minikube service ogofi-web-svc -n "$K8S_NS" --url
        """
      }
    }
  }

  post {
    always {
      sh 'docker logout || true'
      sh 'kubectl get pods -n default|| true'
    }
  }
}
