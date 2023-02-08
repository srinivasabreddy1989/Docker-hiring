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
                sh "docker build . -t srinivasa381224/hiring:${get_commit_id()}"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'Dockerhub-password', variable: 'hubpwd')]) {
                    sh "docker login -u srinivasa381224 -p ${hubpwd}"
                    sh "docker push srinivasa381224/hiring:${get_commit_id()}"
                
            }
        }
    }
    stage('Docker deploy') {
            steps {
                sshagent(['docker-host-elk']) {
                  sh "ssh -o StrictHostKeyChecking=no ec2-user@172.31.21.241 docker rm -f hiring"
                  sh "ssh ec2-user@172.31.21.241 docker run -d -p 8090:8080 --name hiring srinivasa381224/hiring:${get_commit_id()}"
                  sh "ssh ec2-user@172.31.21.241 docker system prune --all --force"
            }
        }
        
    }
    }
}
def get_commit_id(){
    id = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return id
}

