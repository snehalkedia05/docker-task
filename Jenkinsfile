pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "snehalkedia/jenkins-docker-webapp:latest"
        AWS_ACCOUNT_ID = "700049976214"
        AWS_REGION = "us-east-1"
        ECR_REPO = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/snehal-nginx-site"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git url: 'https://github.com/snehalkedia05/docker-task.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE .'
                sh 'docker tag $DOCKER_IMAGE $ECR_REPO:latest'
            }
        }

        stage('DockerHub Login and Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKER_IMAGE
                    '''
                }
            }
        }

        stage('Login to AWS ECR and Push') {
            steps {
                withAWS(credentials: 'aws-creds', region: "${AWS_REGION}") {
                    sh '''
                        aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $ECR_REPO
                        docker push $ECR_REPO:latest
                    '''
                }
            }
        }
    }
}
