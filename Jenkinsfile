pipeline {
    agent any
    environment { 
   VERSION = "${env.BUILD_ID}"
}

    stages {
        stage('Git checkout') {
        steps {
            git branch: 'main', changelog: false, credentialsId: 'github', poll: false, url: 'https://github.com/manish9676/jenkins-kubernetes-example.git'
            
          }
        }

stage('Build docker image') {
            steps {
                sh 'docker build . -t manish9676/nodejsapp-${VERSION}.0:latest'
                sh 'docker images'
            }
        }

stage('Deploy Docker Image') {
            steps {
                script {
                 withCredentials([string(credentialsId: 'manish9676', variable: 'dockerhubpwd')]) {
                    sh 'docker login -u manish9676 -p ${dockerhubpwd}'
                 }
                 sh 'docker push manish9676/nodejsapp-${VERSION}.0:latest'
                }
            }
        }
        
        stage('Replace manifest') {
            steps {
                sh 'pwd'
                sh 'sed "s/{VERSION}/${VERSION}/g" k8s-base/nodejsapp-sed.yaml > k8s-base/nodejsapp.yaml'
            }
        }

        stage('Deploy to K8s') {
            steps {
                kubernetesDeploy configs: './k8s-base/*.yaml', kubeConfig: [path: ''], kubeconfigId: 'K8s', secretName: '', ssh: [sshCredentialsId: '*', sshServer: ''], textCredentials: [certificateAuthorityData: '', clientCertificateData: '', clientKeyData: '', serverUrl: 'https://']
}
}
        }
    }