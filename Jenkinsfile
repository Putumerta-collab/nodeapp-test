pipeline {

  environment {
    dockerimagename = "dpuja/nodeapp"
  }

  agent any

  stages {

    stage('Checkout Source') {
      steps {
        git 'https://github.com/Putumerta-collab/nodeapp_test.git'
      }
    }

    stage('Build Image') {
      steps {
        script {
          dockerImage = docker.build(dockerimagename)
        }
      }
    }

    stage('Push Image') {
      environment {
        registryCredential = 'PUJAREPO'
      }
      steps {
        script {
          docker.withRegistry('https://registry.hub.docker.com', registryCredential) {
            dockerImage.push("latest")
          }
        }
      }
    }

    stage('Deploy App to Kubernetes') {
      steps {
        script {
          withCredentials([file(credentialsId: 'kubeconfig-credentials-id', variable: 'KUBECONFIG')]) {
            // Set the KUBECONFIG environment variable
            env.KUBECONFIG = "${KUBECONFIG}"
            
            // Execute the Kubernetes commands
            sh 'kubectl set image deployment/my-app-deployment my-app-container=${dockerimagename}:latest -n default'
            sh 'kubectl rollout status deployment/my-app-deployment -n default'
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
