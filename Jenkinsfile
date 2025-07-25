pipeline {
    agent any

    environment {
        AWS_REGION = 'us-east-1'
        IMAGE_NAME = 'interactive'   // Note: aapne space diya tha, usko hata diya
        REPO_BASE = '700049976214.dkr.ecr.us-east-1.amazonaws.com/snehal-nginx-site' // aapka AWS account ID aur repo
        IMAGE_TAG = 'latest'
    }

    stages {
        stage('Login to ECR') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', 
                                  credentialsId: 'aws-creds']]) {   // aapke Jenkins me jo AWS creds ka id hai wahi use karen
                    sh '''
                        aws ecr get-login-password --region $AWS_REGION | \
                        docker login --username AWS --password-stdin $REPO_BASE
                    '''
                }
            }
        }

        stage('Tag Image') {
            steps {
                sh 'docker tag $IMAGE_NAME:$IMAGE_TAG $REPO_BASE:$IMAGE_TAG'
            }
        }

        stage('Push to ECR') {
            steps {
                sh 'docker push $REPO_BASE:$IMAGE_TAG'
            }
        }
    }
}
