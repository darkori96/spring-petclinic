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
    //GIT_CREDENTIALS = credentials('gitCredential')
    REGION = 'ap-northeast-2'
  }

  stages {
    stage('Git Clone') {
      steps {
        echo 'Git Clone'
        git url: 'https://github.com/darkori96/spring-petclinic.git',
          branch: 'main'
      }
    }
    //Maven 빌드 작업
    stage('Maven Build') {
      steps {
        echo 'Maven Build'
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
    }
    //Docker Image 생성
    stage('Docker Image Build') {
      steps {
        echo 'Docker Image build'
        dir("${env.WORKSPACE}") {
        sh """
        docker build -t darkori96/spring-petclinic:$BUILD_NUMBER .
        docker tag darkori96/spring-petclinic:$BUILD_NUMBER darkori96/spring-petclinic:latest
        """
        }
      }
    }
    //DockerHub Login
    stage('Docker Login') {
      steps {
        sh """
        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
        docker push darkori96/spring-petclinic:latest
        """
      }
    }
    //Docker Image 삭제
    stage('Remove Docker Image') {
      steps {
        sh """
        docker rmi darkori96/spring-petclinic:$BUILD_NUMBER
        docker rmi darkori96/spring-petclinic:latest
        """
      }
    }
	// S3에 Appspec.yml과 실행 스크립트 저장
        stage('Upload to S3') {
            steps {
                echo "Upload to S3"
                dir("${env.WORKSPACE}") {
                    sh 'zip -r deploy.zip ./deploy AppSpec.yml'
                    withAWS(region:"${REGION}", credentials:"${AWS_CREDENTIALS}"){
                      s3Upload(file:"deploy.zip", bucket:"user05-codedeploy-bucket")
                    } 
                    sh 'rm -rf ./deploy.zip'                 
                }        
            }
        }


  }
}
