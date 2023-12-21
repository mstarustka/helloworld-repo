pipeline {
    tools {
        maven 'Maven3'
    }
    
    agent any

    environment {
        PATH = "$PATH:/usr/local/bin"
        registry = "192.168.4.190:8444/repository/docker-private-repo"
	    DOCKER_IMAGE_NAME = "192.168.4.190:8444/helloworld"
	    dockerImage = ""
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
        stage ('Pull Docker Image From Nexus') {
            steps{  
                script {
                    sh 'docker login 192.168.4.190:8444 --username admin --password=Counterstr1ke'
                    sh 'docker pull 192.168.4.190:8444/helloworld:latest'
                }
            }
        }        
        stage ('Build Docker Image') {
            steps {
                script {
                    dockerImage = docker.build DOCKER_IMAGE_NAME
                    dockerImage.tag("latest")
                }
            }
        }
        stage ('Push Image to Nexus') {
            steps{  
                script {
                    sh 'docker login 192.168.4.190:8444 --username admin --password=Counterstr1ke'
                    sh 'docker push 192.168.4.190:8444/helloworld:latest'
                }
            }
        }  
        stage ('Deploy Helm Chart to Kubernetes Cluster') {
            steps {
                sshagent(credentials: ['2d7cc276-5e9e-4933-93fb-7c6f1a21a9e4']) {
                    sh '''
                        [ -d ~/.ssh ] || mkdir ~/.ssh && chmod 0700 ~/.ssh
                        ssh-keyscan -t rsa,dsa k8scontrol >> ~/.ssh/known_hosts
                        ssh mstarustka@k8scontrol cd /proj/app/helm_deploy
                    '''
                }
            }
//            steps {
//                script {
//                    sh "helm upgrade first --install helloworld-release-dev helloworld/ --values helloworld/values.yaml -f helloworld/values-dev.yaml --namespace dev --set image.tag=latest"
//                }
//            }
        }
    }
    agent {
        def remote = [:]
        remote.name = 'k8scontrol'
        remote.host = 'k8scontrol'
        remote.user = 'mstarustka'
        remote.password = 'Counterstr1ke'
        remote.allowAnyHosts = true
        stage('Remote SSH') {
            sshCommand remote: remote, command: "echo SSH command was successful!"
        }
    }         
}