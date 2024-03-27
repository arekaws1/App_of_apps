def frontendImage="arekaws1/frontend"
def backendImage="arekaws1/backend"
def dockerRegistry=""
def registryCredentials="dockerhub"
def backendDockerTag=""
def frontendDockerTag=""



pipeline {

    parameters {
	

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


