pipeline {
  agent any
  environment {
    IMAGE_NAME = "demo-ci-cd:latest"
  }
  stages {
    stage('Checkout') {
      steps {
        //checkout scm
        git branch: 'main', url: 'https://github.com/Mendoza210727/app3_springboot.git'
      }
    }
    stage('Build & Test') {
      steps {
        sh 'mvn -B clean package'
      }
    }
    stage('Build Docker Image') {
      steps {
        sh 'echo "docker build -t $IMAGE_NAME ."'
      }
    }
    stage('Run Container') {
      steps {
        sh 'echo "docker rm -f demo-ci-cd || true"'
        //sh 'docker rm -f demo-ci-cd || true'
        //sh 'docker run -d --name demo-ci-cd -p 8080:8080 $IMAGE_NAME'
      }
    }
  }
  post {
    always {
      junit '**/target/surefire-reports/*.xml'
      archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
    }
  }
}
