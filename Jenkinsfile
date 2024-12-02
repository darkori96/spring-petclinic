pipeline {
  agent any
  //JAVA와 Maven Tools 등록
  tools {
    jdk 'jdk17'
    maven 'M3'
  }
  enviroment {
    DOCKERHUB_CREDENTIALS = credentials('dockerCredentials')
  }

  stages {
    //GitHub에서 소스코드 복사
    stage('Git Clone') {
      steps{
        git url: 'https://github.com/darkori96/spring-petclinic.git', branch: 'main'
      }
    }
    stage('Maven Build') {
      steps{
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
    }
    stage('Docker Image') {
      steps{
//        dir ('$(env.WORKSPACE)') {
        sh """
        docker build -t darkori96/spring-petclinic:$BUILD_NUMBER .
        docker tag darkori96/spring-petclinic:$BUILD_NUMBER darkori66/spring-petclinic:latest
        """
//      }
      }
    }
    stage('Docker Image Push') {
      steps{
        sh """
        echo $DOCKERHUB_CREDENTAILS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
        docker push darkori96/spring-petclinic:latest
        """
      }
    }
  }
}
