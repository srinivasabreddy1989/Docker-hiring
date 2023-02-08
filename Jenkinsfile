pipeline {
    agent any

    stages {
        
        stage('Maven Build') {
            steps {
                sh "mvn clean package"
            }
        }
        
        stage('Docker Build') {
            steps {
                sh "docker build -t srinivasa381224/hiring:0.0.3 ."
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'Dockerhub-password', variable: 'hubpwd')]) {
                    sh "docker login -u srinivasa381224 -p ${hubpwd}"
                    sh "docker push srinivasa381224/hiring:0.0.3"
                
            }
        }
    }
    stage('Docker deploy') {
            steps {
                sshagent(['docker-host-elk']) {
                  sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.21.4 docker rm -f hiring"
                  sh "ssh -o StrictHostKeyChecking=no ubuntu@172.31.21.4 docker run -d -p 8080:8080 --name hiring srinivasa381224/hiring:0.0.3"
            }
        }
        
    }
    }
}


