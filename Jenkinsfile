pipeline {
  agent any
  environment {
      DOCKERHUB_CREDENTIALS = credentials('DockerHub')
      frontendImageTag = "${BUILD_NUMBER}"
      VERSION = "${env.BUILD_ID}"
  }
  stages{
    stage("Npm Install") {
            steps {
                script {
                    sh "npm install"
                }           
            }
        }
    stage("Test"){
        steps {
         sh "npm run sonar" 
      }
    } 
    stage('Build docker image'){
        steps {              
    //      sh 'docker images'
            sh 'docker build --build-arg configuration=production -t olfarejeb/front-ecommerce:${frontendImageTag} .'
        }
    }
    stage('docker Login & push'){
        steps {
              sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
              sh 'docker push olfarejeb/front-ecommerce:${frontendImageTag} '
        }
    }
    stage('Cleaning up'){
        steps {
               sh "docker rmi olfarejeb/front-ecommerce:$BUILD_NUMBER"
            }
        }
    stage('deploy k8s'){
       steps {
           sh "sed -i 's|__IMAGE_NAME__|olfarejeb/front-ecommerce|g; s|__IMAGE_TAG__|${frontendImageTag}|g' deployement.yml"
           kubernetesDeploy (configs:'deployement.yml', kubeconfigId:'k8sconfigid')
       }
    }
}
 post {
		always {
		mail bcc: '', 
        body: "<br>Project: ${env.JOB_NAME} <br>Build Number: ${env.BUILD_NUMBER} <br> URL de build: ${env.BUILD_URL}",
        cc: '', charset: 'UTF-8', from: '', mimeType: 'text/html', replyTo: '', 
        subject: "${currentBuild.result} CI: Project name -> ${env.JOB_NAME}",
        to: "olfarejeb1@gmail.com";  
		 }
	   }
}
