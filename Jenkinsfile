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

    environment {
      PIP_BREAK_SYSTEM_PACKAGES = 1
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


       stage('cleanup') {
          steps{
            sh "docker rm -f frontend backend"
         }
       }
  

        stage('Deploy application') {
            steps {
                script {
                    withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                             "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                       docker.withRegistry("$dockerRegistry", "$registryCredentials") {
                            sh "docker-compose up -d"
                        }
                    }
                }
            }
        }

       stage('Run selenium'){
         steps {
           sh "pip3 install -r test/selenium/requirements"
           sh "python3 -m pytest test/selenium/frontendTest.py"
         }
       }


  }

   post {
     always {
       sh "docker-compose down"
       cleanWs()
     }
  }
}

