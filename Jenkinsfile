pipeline{
    agent any
    tools {
        maven 'maven'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('git checkout'){
            steps{
                git 'https://github.com/Prasathdv/dockeransiblejenkins.git'
            }
        }
        stage('Maven Build'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Docker Build'){
            steps{
                sh 'docker build . -t prasathdvdevops/javabuildwar:${DOCKER_TAG}'
            }
        }
        stage('Docker Push'){
            steps{
                withCredentials([string(credentialsId: 'prasathdvdevops', variable: 'dockerHubPwd')]) {
                 sh 'docker login -u prasathdvdevops -p ${dockerHubPwd}'
                }
                sh 'docker push prasathdvdevops/javabuildwar:${DOCKER_TAG}'
            }
        }
        stage('Ansible Deploy'){
            steps{
                ansiblePlaybook credentialsId: 'web-server', disableHostKeyChecking: true, extras: '-e DOCKER_TAG=${DOCKER_TAG}', installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
