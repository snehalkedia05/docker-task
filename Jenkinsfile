pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'snehalkedia/docker-task:latest'
        AWS_REGION = 'ap-south-1' // change to your region
        AWS_ECR_REPO = 'snehalkedia-docker-task'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git url: 'https://github.com/snehalkedia05/docker-task.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
            }
        }

        stage('Login to Docker Hub & Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                    echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                    docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Tag for AWS ECR') {
            steps {
                script {
                    sh 'aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.$AWS_REGION.amazonaws.com'
                    sh 'docker tag $DOCKER_IMAGE <aws_account_id>.dkr.ecr.$AWS_REGION.amazonaws.com/$AWS_ECR_REPO:latest'
                }
            }
        }

        stage('Push to AWS ECR') {
            steps {
                sh 'docker push <aws_account_id>.dkr.ecr.$AWS_REGION.amazonaws.com/$AWS_ECR_REPO:latest'
            }
        }
    }
}
