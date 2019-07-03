pipeline {
  environment {
    registry = "ncrmns/helloworld11"
    registryCredential = 'dockerhubncrmns'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Build') {
        steps {
            checkout scm
            sh 'docker build -t ncrmns/helloworld11 .'
        }
    }
    stage('Test'){
        steps{
            echo 'Test complete'
        }
    }
    stage('Building DockerImage') {
      steps{
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
    }
    stage ('==>DockerHub') {
			when {
				branch 'master'
			}
			steps {
				script {
					docker.withRegistry( '', registryCredential ) {
						dockerImage.push()
					}
				}
			}
		}
    stage ('==>AWS') {
			when {
				branch 'master'
			}
      steps {
        sh 'mkdir git'
        sh 'cp ./Dockerrun.aws.json ./AWS/Dockerrun.aws.json'
        withAWS(region:'us-east-1',credentials:'awsebcred') {
          s3Upload(bucket: 'elasticbeanstalk-us-east-1-124429370407/helloworld11', workingDir:'./AWS', includePathPattern:'**/*');
				}
        echo 'complete'
			}
		}
  }
}