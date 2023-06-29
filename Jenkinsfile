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
        sh 'docker push eden266/node-app:v2'
        sh 'ssh opes@10.0.10.2'
        sh 'hostname'
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
    
    // node ("ssh") {
    //   def remote = [:]
    //   remote.name = 'test-kmc01'
    //   remote.host = '10.0.10.2'
    //   remote.user = 'opes'
    //   remote.password = 'Hanoi@123'
    //   remote.allowAnyHosts = true
    //   stage('Remote SSH') {
    //     sshCommand remote: remote, command: "ls -lrt"
    //     sshCommand remote: remote, command: "for i in {1..5}; do echo -n \"Loop \$i \"; date ; sleep 1; done"
    //   }
    // }
      stage('Deploy Dev') {
        
        //   script {
              // withCredentials(bindings: [usernamePassword(credentialsId: DOCKERHUB_CREDENTIALS, usernameVariable: 'DOCKER_CREDENTIAL_USER', passwordVariable: 'DOCKER_CREDENTIAL_PSW')]) {
              //   sh 'kubectl delete secret regcred --ignore-not-found'
              //   sh 'kubectl create secret docker-registry regcred'
              // }
              // sh "helm upgrade --set image.tag=${commitId} --install --wait dev-example-service ./chart --namespace example-dev"
        steps{
          script {
            sshagent(credentials : ['my-ssh-key']) {
                sh 'ssh -o StrictHostKeyChecking=no -i my-ssh-key opes@10.0.10.2 "hostname"'
                // sh 'ssh -v opes@10.0.10.2'
                // sh 'scp ./test opes@10.0.10.2:/home/opes'
                sh 'cd node-git-to-k8s'
                sh "helm upgrade --install jenkins-nodejs ./node-app-chart"
            }
          }
        }
         
        
      }
      
  }
  post {
    always {
      sh 'docker logout'
    }
  }
}
