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
                sh "docker build -t kammana/hiring:0.0.2 ."
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'Dockerhub-password', variable: 'hubpwd')]) {
                    sh "docker login -u srinivasa381224 -p ${hubpwd}"
                    sh "docker push kammana/hiring:0.0.2"
                
            }
        }
        
    }
    }
}


