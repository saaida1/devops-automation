pipeline {
    agent any
    tools {
        maven 'maven_3_5_0'
    }
    stages {
        stage('Build Maven') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[url: 'https://github.com/saaida1/devops-automation']]])
                sh 'mvn clean install'
            }
        }
        stage('Build docker image') {
            steps {
                script {
                    sh 'docker build -t saaida/devops-integration .'
                }
            }
        }
        stage('Push image to Hub') {
            steps {
                script {
                    withCredentials([string(credentialsId: 'dockerhub-pwd', variable: 'dockerhubpwd')]) {
                        sh 'docker login -u saaida -p ${dockerhubpwd}'
                    }
                    sh 'docker push saaida/devops-integration'
                }
            }
        }
        stage('Deploy to k8s') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'k8sconfigpwd']) {
                        sh 'kubectl apply -f deploymentservice.yaml' 
                    }
                }
            }
        }
    }
}
