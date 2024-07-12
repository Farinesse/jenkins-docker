pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('Farines-dockerhub')
    }
    stages {
        stage('Build docker image') {
            steps {
                sh 'docker build -t myapp/flask:$BUILD_NUMBER .'
            }
        }
        stage('Login to Docker Hub') {
            steps {
                script {
                    def dockerhubUsername = env.DOCKERHUB_CREDENTIALS_USR
                    def dockerhubPassword = env.DOCKERHUB_CREDENTIALS_PSW
                    sh "echo $dockerhubPassword | docker login -u $dockerhubUsername --password-stdin"
                }
            }
        }
        stage('Push image') {
            steps {
                sh 'docker push myapp/flask:$BUILD_NUMBER'
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
