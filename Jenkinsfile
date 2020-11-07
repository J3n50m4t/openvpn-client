// Startet im Jenkins eine Multibranch Pipeline 
pipeline {
  agent any
  environment {
      registry = "mdittrichlp/openvpn-client"
      registryCredential = 'dockerhubmDittrich'
      dockerImage = ''
  }

  stages {
    stage('Checkout SCM') {
      steps {
        checkout([
          $class: 'GitSCM',
          branches: [[name: '*/master']],
          userRemoteConfigs: [[
            credentialsId: 'GitHubMachineJessy', 
            url: 'https://github.com/J3n50m4t/openvpn-client'
          ]]
        ])
      }
    } // stage('Checkout SCM')

    stage ('Build Docker Image') {
      steps {
        script {
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
          dockerImageLatest = docker.build registry + ":latest"
        }
      } //steps
    } // stage ('Build Docker Image')
    
    stage('Deploy Image') {
      steps{    
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
            dockerImageLatest.push()
          } //docker.withRegistry
        } // script
      } // steps
    } // stage('Deploy Image')
  } //stages

  post {
    unsuccessful {
      emailext attachLog: true, compressLog: true, body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
      recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
      subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
    }
    
    fixed {
      emailext body: "${currentBuild.currentResult}: Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}",
      recipientProviders: [[$class: 'DevelopersRecipientProvider'], [$class: 'RequesterRecipientProvider']],
      subject: "Jenkins Build ${currentBuild.currentResult}: Job ${env.JOB_NAME}"
    }
  } // post
} // pipeline