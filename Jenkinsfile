pipeline
{
    agent any

    environment {
        IMAGE_NAME = "30balachandar333/devops-evaluation"
    }

    stages{
        stage('Checkout Source') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Balachandar-2001/DevOps-Evaluation.git'
            }
        }

        stage('Containerize React App') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${BUILD_NUMBER} ."
            }
        }

        stage('Tag the image as latest') {
            steps{
                sh "docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest"
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                    sh "docker push ${IMAGE_NAME}:latest"
                }
            }
        }

        stage('Deploy to the Kube Cluster'){
            steps {
                sh """
                  kubectl set image deployment/devops-evaluation devops-evaluation=${IMAGE_NAME}:${BUILD_NUMBER}
                  kubectl rollout status deployment/devops-evaluation              
                """
            }
        }
    }

    post{
        always{
            cleanWs()
        }
        success{
            echo "Build completed successfully."
        }
        failure{
            echo "Build failed."
        }
    }
}