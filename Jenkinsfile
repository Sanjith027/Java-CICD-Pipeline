pipeline {
  agent any

  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
  }

  stages {
    stage('Clean Workspace') {
      steps {
        cleanWs()
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
