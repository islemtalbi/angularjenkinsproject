def getVersion(){
    def version = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return version.trim()
}
pipeline {
    agent { label 'jenkins' }
    environment {
        DOCKER_TAG = getVersion()
    }
    stages {
        stage ('Clone Stage') {
            steps {
                git branch: 'main', url: 'https://github.com/islemtalbi/angularjenkinsproject.git'
            }
        }
        stage ('Docker Build') {
            steps {
                sh "docker build -t islemtalbidev/aston-villa-app:${DOCKER_TAG} ."
            }
        }
        stage ('DockerHub Push') {
            steps {
                withCredentials([string(credentialsId: 'mydockerhubpassword', variable: 'DockerHubPassword')]) {
                    sh "docker login -u islemtalbidev -p ${DockerHubPassword}"
                }
                sh "docker push islemtalbidev/aston-villa-app:${DOCKER_TAG}"
            }
        }
        stage ('Deploy') {
            steps {
                sshagent(credentials: ['Vagrant_ssh']) {
                    sh "ssh -o StrictHostKeyChecking=no islemtalbi@192.168.56.10 'docker pull islemtalbidev/aston-villa-app:${DOCKER_TAG} && docker run -d -p 80:80 islemtalbidev/aston-villa-app:${DOCKER_TAG}'"
                }
            }
        }
    }
}
