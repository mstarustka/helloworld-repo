pipeline {
    environment {
        registry = "192.168.4.190:8444/repository/docker-private-repo"
	dockerimagename = "mstarustka/helloworld"
	dockerImage = ""
    }
    agent any
    stages {
        stage('Cloning Git') {
            steps {
                git branch: 'main', url: 'https://github.com/mstarustka/helloworld-repo.git'
            }
        }
      stage ('Build') {
          steps {
            sh 'mvn clean install'           
            }
      }
    // Building Docker images
    stage('Building image') {
      steps{
        script {
          dockerImage = docker.build helloworld 
          dockerImage.tag("latest")
        }
      }
    }
   
    // Uploading Docker images into NEXUS Repository
    stage('Pushing to Nexus') {
     steps{  
         script {
                sh 'docker login 192.168.4.190:8444 --username admin --password=Counterstr1ke'
                sh 'docker push 192.168.4.190:8444/helloworld:latest'
         }
        }
      }
        stage ('Helm Deploy') {
          steps {
            script {
                sh "helm upgrade first --install helloworld-release-dev helloworld/ --values helloworld/values.yaml -f helloworld/values-dev.yaml --namespace dev --set image.tag=latest"
                }
            }
        }
    }
}
