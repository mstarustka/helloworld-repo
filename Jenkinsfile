pipeline {
    tools {
        maven 'Maven3'
    }
    environment {
        registry = "192.168.4.190:8444/repository/docker-private-repo"
	      dockerimagename = "mstarustka/helloworld"
	      dockerImage = ""
    }
    
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
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
        stage ('Test') {
            steps {
                sh 'node --version'
            }
        }
        stage('Building image') {
            steps {
              script {
                dockerImage = docker.build dockerimagename
                dockerImage.tag("latest")
              }
            }
        }
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