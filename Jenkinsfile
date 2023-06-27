pipeline {
  agent any
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))
  }
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerhub')
    // REGISTRY = 'gitlab-jenkins.opes.com.vn'
    // the project name
    // make sure your robot account have enough access to the project
    // HARBOR_NAMESPACE = 'jenkins-harbor'
    // docker image name
    // APP_NAME = 'docker-example'
    // ‘robot-test’ is the credential ID you created on the KubeSphere console
    // HARBOR_CREDENTIAL = credentials('harbor')
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t eden266/node-app:v2 .'
        // sh 'docker build -t $REGISTRY/$HARBOR_NAMESPACE/$APP_NAME:jenkins-nodejs .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
        // sh '''echo $HARBOR_CREDENTIAL_PSW | docker login $REGISTRY -u 'admin' --password-stdin'''
      }
    }
    stage('Push') {
      environment {
        registryCredential = 'dockerhub'
      }
      steps {
        sh 'docker push eden266/jenkins-nodejs:v2'
        // sh 'docker push  $REGISTRY/$HARBOR_NAMESPACE/$APP_NAME:jenkins-nodejs'
        /*
        script {
          
          docker.withRegistry( 'https://gitlab-jenkins.opes.com.vn', registryCredential ) {
            sh 'docker push  $REGISTRY/$HARBOR_NAMESPACE/$APP_NAME:jenkins-nodejs'
          }
        }
        */
      }
    }
    /*
    stage('Deploy Dev') {
      when {
        branch 'main'
      }
      environment {
        registryCredential = 'harbor'
      }
      steps {
        script{
          commitId = sh(returnStdout: true, script: 'git rev-parse --short HEAD')
          commitId = commitId.trim()
          withKubeConfig(credentialsId: 'kubeconfig') {
            withCredentials(bindings: [usernamePassword(credentialsId: registryCredential, usernameVariable: 'HARBOR_CREDENTIAL_USER', passwordVariable: 'HARBOR_CREDENTIAL_PSW')]) {
              sh 'kubectl delete secret regcred --namespace=jenkins-nodejs --ignore-not-found'
              sh 'kubectl create secret docker-registry regcred --namespace=jenkins-nodejs --docker-server=https://gitlab-jenkins.opes.com.vn --docker-username=$HARBOR_CREDENTIAL_USER --docker-password=$HARBOR_CREDENTIAL_PSW --docker-email=email@example.com'
            }
            sh "helm upgrade --set image.tag=${commitId} --install --wait dev-example-service ./chart --namespace example-dev"
          }
        }
      }
    }
    */
    
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
