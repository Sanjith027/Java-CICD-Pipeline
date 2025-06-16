pipeline {
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }

  options {
    skipDefaultCheckout(true)  // 👈 prevents default checkout
  }

  stages {
    stage('Clean Workspace') {
      steps {
        cleanWs()
      }
    }

    stage('Checkout Source') {
      steps {
        checkout scm  // 👈 manually do the git clone AFTER workspace is clean
      }
    }

    stage('Build with Maven') {
      steps {
        sh 'chmod +x mvnw'
        sh './mvnw clean package'
      }
    }

    stage('Build Docker Image') {
      steps {
        sh 'docker build -t sanjith027/spring-petclinic .'
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker push sanjith027/spring-petclinic
          '''
        }
      }
    }
  }
}
