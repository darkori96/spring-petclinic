pipeline {
  agent any
  //JAVA와 Maven Tools 등록
  tools {
    jdk 'JDK17'
    maven 'M3'
  }
  //접속 정보
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerCredential')
    AWS_CREDENTIALS = credentials('AWSCredential')
    GIT_CREDENTIALS = credentials('gitCredential')
    REGION = 'ap-northeast-2'
  }

  stages {
    stage('Git Clone') {
      steps {
        echo 'Git Clone'
        git url: 'https://github.com/darkori96/spring-petclinic.git',
          branch: 'main', credentialsId: 'GIT_CREDENTIALS'
      }
    }
  }
}
