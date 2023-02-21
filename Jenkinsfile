pipeline {
    agent any

    stages {
        
        stage('Maven Build') {
            steps {
                sh "mvn clean package"
            }
        }
        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar9') { 
                sh "mvn sonar:sonar"
            }
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
    
    // Email notification for success and failure
//     post {
//         success {
//             emailext to: "srinivasa20071989@gmail.com",
//             subject: "jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}",
//             body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}"
//   }
//   failure {
//     emailext to: "srinivasa20071989@gmail.com",
//     subject: "jenkins build:${currentBuild.currentResult}: ${env.JOB_NAME}",
//     body: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}"
//   }
// }
// Slack notification for success and failure
    post {
        success {
    slackSend channel: '#czv', color: 'green', failOnError: true, message: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}", tokenCredentialId: 'slack-token'
    }
        failure {
            slackSend channel: '#czv', color: 'red', failOnError: true, message: "${currentBuild.currentResult}: Job ${env.JOB_NAME}\nMore Info can be found here: ${env.BUILD_URL}", tokenCredentialId: 'slack-token'
        }
    }
}
def get_commit_id(){
    id = sh returnStdout: true, script: 'git rev-parse --short HEAD'
    return id
}

