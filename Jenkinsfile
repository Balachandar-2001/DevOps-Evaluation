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

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                    sh "docker push ${IMAGE_NAME}:${BUILD_NUMBER}"
                }
            }
        }

        stage('Pull Docker Image') {
            steps {
                sh "docker pull ${IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }

        stage('Stop and Remove Existing Container') {
            steps {
                sh "docker stop devops-evaluation || true"
                sh "docker rm -f devops-evaluation || true"
            }
        }

        stage('Run Docker Container') {
            steps {
                sh "docker run --name devops-evaluation -p 5000:80 ${IMAGE_NAME}:${BUILD_NUMBER}"
            }
        }

        stage('docker ps') {
            steps {
                sh "docker ps"
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