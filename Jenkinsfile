pipeline {
  agent any
  //JAVA와 Maven Tools 등록
  tools {
    jdk 'jdk17'
    maven 'M3'
  }
  environment {
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
        docker tag darkori96/spring-petclinic:$BUILD_NUMBER darkori96/spring-petclinic:latest
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
    stage('Remove Docker Image') {
      steps{
        sh """
        docker rmi darkori96/spring-petclinic:$BUILD_NUMBER
        docker rmi darkori96/spring-petclinic:latest
        """
      }
    }
    stage('Docker Container') {
      steps{
        sh """
        sshPublisher(publishers: [sshPublisherDesc(configName: 'target', 
        transfers: [sshTransfer(cleanRemote: false, 
        excludes: '', 
        execCommand: '''
        docker rm -f $(docker ps -aq)
        docker rmi $(docker images -q)
        docker run -d -p 80:8080 --name spring-petclinic darkori96/sdpring-petclinic:latest
        ''',
        export BUILD_ID=Petclinic-Pipeline
        nohup java -jar /home/ubuntu/spring-petclinic-3.3.0-SNAPSHOT.jar >> nohup.out 2>&1 &''', 
        execTimeout: 120000, 
        flatten: false, 
        makeEmptyDirs: false, 
        noDefaultExcludes: false, 
        patternSeparator: '[, ]+', 
        remoteDirectory: '', 
        remoteDirectorySDF: false, 
        removePrefix: 'target', 
        sourceFiles: 'target/*.jar')], 
        usePromotionTimestamp: false, 
        useWorkspaceInPromotion: false, 
        verbose: false)])
        """
      }
    }
  }
}
