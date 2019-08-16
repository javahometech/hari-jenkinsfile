def ecrRepoName = "ecr_docker_repository"
def version = "latest"
def region = "us-west-2"
pipeline {
    agent any
    stages {
        stage('Git Checkout') {
            steps {
            checkout(scm)
            }
        }
        stage('Create & Push DockerImage') {
            steps {
            script{
              def repo_url = makeSureECRExists(ecrRepoName, region)
              echo repo_url
               def repoTag = repo_url+":"+"latest"
            sh "\$(aws ecr get-login --no-include-email --region ${region})"
                sh "docker build -t ${repoTag} ."
            sh "docker push ${repoTag}"
            }
          }
        }
    }
}

def makeSureECRExists(ecrRepoName, region){
  try{
    def repoUrl = sh script:"aws ecr create-repository --repository-name ${ecrRepoName} --region ${region} --output text | awk '{print \$NF}'",  returnStdout: true
    return repoUrl
  }catch(error){
    echo "INFO repository already exists"
    def repoUrl = sh script:"aws ecr describe-repositories --repository-name ${ecrRepoName} --region ${region} --output text | awk '{print \$NF}'", returnStdout: true
    return repoUrl
  }
}
