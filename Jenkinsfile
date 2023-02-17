pipeline {
  agent any
  environment {
    GITHUB_CREDENTIALS = credentials('linhduonghygithub')
    DOCKERHUB_CREDENTIALS = credentials('linhduonghydockerhub')
    ARGOCD_CREDENTIALS = credentials('sondnargocd')
  }
  stages {
    stage('Build') {
      steps {
        sh 'docker build -t heart:latest .'
      }
    }
    stage('Login') {
      steps {
        sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
      }
    }
    stage('Push') {
      steps {
        sh 'docker tag heart:latest linhduonghy/heart'
        sh 'docker push linhduonghy/heart'
      }
    }
    stage("Create app in Argocd") {
            steps {
                sh "/usr/local/bin/argocd login 34.136.50.11:443 --insecure --username $ARGOCD_CREDENTIALS_USR --password $ARGOCD_CREDENTIALS_PSW"
                script {
                    // sh "pwd"
                    sh '''
                        #!/bin/bash
                        /usr/local/bin/argocd app create heart --upsert --sync-policy automated --sync-option CreateNamespace=true --repo https://github.com/linhduonghy/heart.git --path . --dest-namespace eurekaserver --dest-server https://kubernetes.default.svc --directory-recurse
                    '''
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