pipeline {
  environment {
    registry = "iceoid/docker-test"
    ghcr = "ghcr.io/iceoid/jenkins-test"
    registryCredential = 'DOCKERHUB_TOKEN'
    githubCredentials = 'GITHUB_TOKEN'
    
    dockerImage = ''
    dockerImageLatest = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git branch: 'main', credentialsId: 'GITHUB_TOKEN', url: 'https://github.com/Iceoid/minecraft-bedrock-server.git'
      }
    }
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
          dockerImageLatest = docker.build registry + ":latest"
          
          dockerImage_ghcr = docker.build ghcr + ":latest"
        }
      }
    }
    stage('Deploy Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            dockerImageLatest.push()
          }
          docker.withRegistry('https://ghcr.io/iceoid/jenkins-test', githubCredentials) {
            dockerImage_ghcr.push()
          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$BUILD_NUMBER"
        sh "docker rmi $registry:latest"
        sh "docker rmi $ghcr:latest"
      }
    }
  }
}
