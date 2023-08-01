pipeline {
  agent any
  environment {
      DOCKERHUB_CREDENTIALS = credentials('DockerHub')
      frontendImageTag = "${BUILD_NUMBER}"
  }
  stages{
    stage("Npm Install") {
            steps {
                script {
                    sh "npm install"
                }           
            }
        }
    stage('Build docker image'){
        steps {              
    //      sh 'docker images'
            sh 'docker build --build-arg configuration=production -t olfarejeb/front-ecommerce:${frontendImageTag} .'
        }
    }
    stage('docker Login & push') {
        steps {
              sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
              sh 'docker push olfarejeb/front-ecommerce:${frontendImageTag} '
        }
    }
    stage('deploy k8s'){
       steps {
           sh "sed -i 's|__IMAGE_NAME__|olfarejeb/front-ecommerce|g; s|__IMAGE_TAG__|${frontendImageTag}|g' deployement.yml"
           kubernetesDeploy (configs:'deployement.yml', kubeconfigId:'k8sconfigid')
       }
    }
}
}
