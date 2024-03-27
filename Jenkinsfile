def frontendImage="arekaws1/frontend"
def backendImage="arekaws1/backend"
def dockerRegistry=""
def registryCredentials="dockerhub"
def backendDockerTag=""
def frontendDockerTag=""



pipeline {

    parameters {
        string(name: 'backendDockerTag', defaultValue: '', description: 'Backend docker image tag')
        string(name: 'frontendDockerTag', defaultValue: '', description: 'Frontend docker image tag')
    }

    agent {
        label 'agent'
    }
    stages {
        stage('Checkout from GITHUB') {
            steps {
                checkout scm
            }
        }

	stage('Adjust version') {
            steps {
                script{
                    backendDockerTag = params.backendDockerTag.isEmpty() ? "latest" : params.backendDockerTag
                    frontendDockerTag = params.frontendDockerTag.isEmpty() ? "latest" : params.frontendDockerTag
                   
                    currentBuild.description = "Backend: ${backendDockerTag}, Frontend: ${frontendDockerTag}"
                }
            }

    }
}
}

