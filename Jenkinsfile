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
                
                    sh "docker login -u kammana -p xxxxxxx"
                    sh "docker push kammana/hiring:0.0.2"
                
            }
        }
        
        

    }
}


