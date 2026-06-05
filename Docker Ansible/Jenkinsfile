pipeline{
    agent any
    tools {
        maven 'maven3'
    }
    environment {
        DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git branch: 'jekinsfile', url: 'https://github.com/Manash2712/Docker-Ansible-Jenkins'
            }
        }
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        stage('Docker Build'){
            steps{
                sh "docker build . -t manashchauhan/jenkinsansible:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'DockerHubPW', variable: 'dockerhubpw')]) {
                    sh "docker login -u manashchauhan -p ${dockerhubpw}"
                }
                sh "docker push manashchauhan/jenkinsansible:${DOCKER_TAG} "
            }
        }
        stage('DockerHub Deploy'){
            steps{
                ansiblePlaybook credentialsId: 'dev-server', 
                disableHostKeyChecking: true, 
                extras: "-e DOCKER_TAG=${DOCKER_TAG}", 
                installation: 'ansible', 
                inventory: 'dev.inv', 
                playbook: 'deploy-docker.yml', 
                vaultTmpPath: ''
            }
        }
    }
}

def getVersion(){
    def commitHash = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
