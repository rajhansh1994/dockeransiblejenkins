pipeline{
    agent any
    tools {
      maven 'MAVEN'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage('SCM'){
            steps{
                git url: 'https://github.com/rajhansh1994/dockeransiblejenkins'
                    
            }
        }
        
        stage('Maven Build'){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build'){
            steps{
                sh "docker build . -t rajhansh/jenkinlearn:${DOCKER_TAG} "
            }
        }
        
        stage('DockerHub Push'){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'dockerHubPwd')]) {
                    sh "docker login -u rajhansh -p ${dockerHubPwd}"
                }
                
                sh "docker push rajhansh/jenkinlearn:${DOCKER_TAG} "
            }
        }
        
        stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: 'dev-server', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
    }
}

def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
