pipeline {
    agent any

    environment {
        SONARQUBE = 'SonarQube' // Matches the name in Jenkins System config
    }

    tools {
        maven 'Maven 3.9.6'
        sonarQubeScanner 'SonarScanner'
    }

    stages {
        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout Source') {
            steps {
                checkout scm
            }
        }

        stage('Code Quality - SonarQube') {
            steps {
                withCredentials([string(credentialsId: 'my-sonarqube-token', variable: 'SONAR_TOKEN')]) {
                    withSonarQubeEnv("${SONARQUBE}") {
                        sh """
                        sonar-scanner \
                          -Dsonar.projectKey=spring-petclinic \
                          -Dsonar.sources=src \
                          -Dsonar.java.binaries=target \
                          -Dsonar.login=$SONAR_TOKEN
                        """
                    }
                }
            }
        }

        stage('Build with Maven') {
            steps {
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t sanjith27/spring-petclinic .'
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push sanjith27/spring-petclinic'
                }
            }
        }
    }
}
