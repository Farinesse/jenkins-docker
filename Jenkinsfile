pipeline {
    agent any
    environment {
        DOCKERHUB_CREDENTIALS = credentials('Farines-dockerhub')
    }
    stages {
        stage('Build docker image') {
            steps {
                sh 'docker build -t farines/flask:$BUILD_NUMBER .'
            }
        }
        stage('Scan Docker Image with Trivy') {
            steps {
                script {
                    try {
                        def trivyOutput = sh(script: "trivy image farines/flask:$BUILD_NUMBER", returnStdout: true).trim()
                        println trivyOutput
                        if (trivyOutput.contains("Total: 0")) {
                            echo "No vulnerabilities found in the Docker image."
                        } else {
                            echo "Vulnerabilities found in the Docker image."
                            // Optionally fail the build if vulnerabilities are found
                            // error "Vulnerabilities found in the Docker image."
                        }
                    } catch (Exception e) {
                        echo "Trivy scan failed: ${e.message}"
                        error "Stopping the build due to Trivy scan failure."
                    }
                }
            }
        }
        stage('login to dockerhub') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin'
            }
        }
        stage('push image') {
            steps {
                sh 'docker push farines/flask:$BUILD_NUMBER'
            }
        }
    }
    post {
        always {
            sh 'docker logout'
        }
    }
}
