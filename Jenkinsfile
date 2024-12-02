pipeline {
  agent any
  //JAVA와 Maven Tools 등록
  tools {
    jdk 'jdk17'
    maven 'M3'
  }

  stages {
    //GitHub에서 소스코드 복사
    stage('Git Clone') {
      steps{
        git url: 'https://github.com/darkori96/spring-petclinic.git', branch: 'main'
      }
    }
  }
}
