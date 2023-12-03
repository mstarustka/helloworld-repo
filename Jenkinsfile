pipeline {
   tools {
        maven 'Maven3'
    }
    agent any
    environment {
        registry = "192.168.4.190:8444/repository/docker-private-repo"
    }
   
    stages {
        stage('Cloning Git') {
            steps {
                git 'https://github.com/mstarustka/helloworld-repo.git'
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
          dockerImage = docker.build registry 
          dockerImage.tag("$BUILD_NUMBER")
        }
      }
    }
   
    // Uploading Docker images into NEXUS Repository
    stage('Pushing to Nexus') {
     steps{  
         script {
                sh 'docker login 192.168.4.190:8444 --username admin --password=Counterstr1ke'
                sh 'docker push 192.168.4.190:8444/helloworld:$BUILD_NUMBER'
         }
        }
      }
        stage ('Helm Deploy') {
          steps {
            script {
                sh "helm upgrade first --install helloworld-release-dev helloworld/ --values helloworld/values.yaml -f helloworld/values-dev.yaml --namespace dev --set image.tag=$BUILD_NUMBER"
                }
            }
        }
    }
}
