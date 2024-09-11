pipeline {

  environment {
    KUBECONFIG = credentials('kubeconfig-credentials-id')
    dockerimagename = "dpuja/nodeapp"
    dockerImage = ""
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/Putumerta-collab/nodeapp_test.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build dockerimagename
        }
      }
    }

    stage('Pushing Image') {
      environment {
        registryCredential = 'PUJAREPO'
      }
      steps{
        script {
          docker.withRegistry( 'https://registry.hub.docker.com', registryCredential ) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploying App to Kubernetes') {
      steps {
        script {
          withCredentials([file(credentialsId: "kubeconfig-credentials-id", variable: 'KUBECONFIG')]) {
            // Update deployment Kubernetes dengan image baru
            sh """
            kubectl --kubeconfig=$KUBECONFIG set image deployment/my-app-deployment my-app-deployment=${dockerimagename}:latest -n default
            kubectl --kubeconfig=$KUBECONFIG rollout status deployment/my-app-deployment -n default
            """
          }
        }
      }
    }

  }

  post {
    always {
      // Optional cleanup steps
      sh 'docker system prune -f'
    }
  }

}
