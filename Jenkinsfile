pipeline {
  agent any
  environment {
    IMAGE = "adamyasrivastava/mysite"
    TAG   = "${BUILD_NUMBER}"
  }
  stages {
    stage('Checkout') {
      steps { checkout scm }
    }
    stage('Build Image') {
      steps { sh 'docker build -t $IMAGE:$TAG .' }
    }
    stage('Push Image') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
          usernameVariable: 'U', passwordVariable: 'P')]) {
          sh 'echo $P | docker login -u $U --password-stdin'
          sh 'docker push $IMAGE:$TAG'
        }
      }
    }
    stage('Deploy to K8s') {
      steps {
        sh "sed -i 's/IMAGE_TAG/$TAG/' k8s/deployment.yaml"
        sh 'kubectl apply -f k8s/'
        sh 'kubectl rollout status deployment/mysite'
      }
    }
  }
}
