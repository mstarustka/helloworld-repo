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
//            steps {
//                sshagent(credentials: ['jenkins']) {
//                    sh '''
//                        [ -d ~/.ssh ] || mkdir ~/.ssh && chmod 0700 ~/.ssh
//                        ssh-keyscan -t rsa,dsa k8scontrol >> ~/.ssh/known_hosts 
//                        ssh jenkins@k8scontrol echo "Successfully connected to k8scontrol."
//                        ssh jenkins@k8scontrol hostname
//                        ssh jenkins@k8scontrol "cd /proj/app/helm_deploy; pwd; scp -r jenkins@jenkins:/home/jenkins/workspace/Deploy_Helm_Chart_main/helloworld ./"
//                    '''
//                }
//            }
            steps {
                withKubeCredentials(kubectlCredentials: [[caCertificate: '', clusterName: 'microk8s', contextName: '', credentialsId: 'kommander-cluster-admin-secret', namespace: '', serverUrl: 'https://192.168.4.185:16443']]) {
                    sh 'kubectl get all -n dev'
                    sh 'helm list -n dev'
                }
            }
        }         
    }
}