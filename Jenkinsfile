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
          set -eux
          docker build -t "${DOCKERHUB_REPO}:${BUILD_NUMBER}" .
          docker tag "${DOCKERHUB_REPO}:${BUILD_NUMBER}" "${DOCKERHUB_REPO}:latest"
        """
      }
    }

    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
          sh """
            set -eux
            echo "\$DH_PASS" | docker login -u "\$DH_USER" --password-stdin
            docker push "${DOCKERHUB_REPO}:${BUILD_NUMBER}"
            docker push "${DOCKERHUB_REPO}:latest"
          """
        }
      }
    }

    stage('Deploy (Apply YAML)') {
      steps {
        sh """
          set -eux
          kubectl apply -f k8s/deployment.yaml -n "${K8S_NS}"
          kubectl apply -f k8s/service.yaml -n "${K8S_NS}"
        """
      }
    }

    stage('Update Image + Rollout') {
      steps {
        sh """
          set -eux
          kubectl set image deployment/${K8S_DEPLOY} web="${DOCKERHUB_REPO}:${BUILD_NUMBER}" -n "${K8S_NS}"
          kubectl rollout status deployment/${K8S_DEPLOY} -n "${K8S_NS}" --timeout=180s
        """
      }
    }

    stage('Show URL') {
      steps {
        sh """
          set -eux
          kubectl get svc ogofi-web-svc -n "${K8S_NS}" -o wide
          minikube service ogofi-web-svc -n "${K8S_NS}" --url
        """
      }
    }
  }

  post {
    always {
      sh 'docker logout || true'
      sh 'kubectl get pods -n default || true'
    }
  }
}