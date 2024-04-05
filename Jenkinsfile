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


    {
      terraform 'Terraform'
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
           sh "pip3 install -r test/selenium/requirements.txt"
           sh "python3 -m pytest test/selenium/frontendTest.py"
         }
       }


       stage('Run terraform') {
            steps {
                dir('Terraform') {                
                    git branch: 'main', url: 'https://github.com/arekaws1/Terraform'
                    withAWS(credentials:'AWS', region: 'us-east-1') {
                            sh 'terraform init -backend-config=bucket=arkadiusz-bigos-panda-devops-core-17'
                            sh 'terraform apply -auto-approve -var bucket_name=arkadiusz-bigos-panda-devops-core-17'
                            
                    } 
                }
            }
       }

       stage('Run Ansible') {
               steps {
                   script {
                        sh "pip3 install -r requirements.txt"
                        sh "ansible-galaxy install -r requirements.yml"
                        withEnv(["FRONTEND_IMAGE=$frontendImage:$frontendDockerTag", 
                                 "BACKEND_IMAGE=$backendImage:$backendDockerTag"]) {
                            ansiblePlaybook inventory: 'inventory', playbook: 'playbook.yml'
                        }
                }
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

