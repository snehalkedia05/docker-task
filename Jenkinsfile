pipeline {
    agent any

    environment {
        DOCKERHUB_IMAGE = "snehalkedia/jenkins-docker-webapp:latest"
        AWS_REGION = "ap-south-1"
        AWS_ECR_URI = "123456789012.dkr.ecr.ap-south-1.amazonaws.com/jenkins-docker-webapp"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git url: 'https://github.com/snehalkedia05/jenkins-docker-webapp.git', branch: 'main'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKERHUB_IMAGE ."
                sh "docker tag $DOCKERHUB_IMAGE $AWS_ECR_URI"
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh '''
                        echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin
                        docker push $DOCKERHUB_IMAGE
                    '''
                }
            }
        }

        stage('Login to ECR') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'aws-creds']]) {
                    sh '''
                        aws configure set aws_access_key_id $AWS_ACCESS_KEY_ID
                        aws configure set aws_secret_access_key $AWS_SECRET_ACCESS_KEY
                        aws configure set region $AWS_REGION
                        aws ecr get-login-password --region $AWS_REGION | docker login --username AWS --password-stdin $AWS_ECR_URI
                    '''
                }
            }
        }

        stage('Push to ECR') {
            steps {
                sh "docker push $AWS_ECR_URI"
            }
        }
    }
}
