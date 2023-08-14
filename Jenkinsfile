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
    stage("Test") {
        steps {
        sh "sonar-scanner" \
         -Dsonar.projectKey=sonarqube \
         -Dsonar.host.url=http://192.168.2.128:9000 \
         -Dsonar.login= 78824419692dc227ac7bff870323f3f2470fb0ba 
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
