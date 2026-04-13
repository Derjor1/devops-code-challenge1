pipeline {
    agent any

    environment {
        AWS_REGION = "us-east-2"
        ACCOUNT_ID = "883627150323"

        FRONTEND_REPO = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/devops-challenge-frontend"
        BACKEND_REPO  = "${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/devops-challenge-backend"

        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Login to ECR') {
            steps {
                sh """
                aws ecr get-login-password --region ${AWS_REGION} | \
                docker login --username AWS --password-stdin ${ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com
                """
            }
        }

        stage('Build Backend') {
            steps {
                sh """
                docker build -t backend-app ./backend
                docker tag backend-app:latest ${BACKEND_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Build Frontend') {
            steps {
                sh """
                docker build -t frontend-app ./frontend
                docker tag frontend-app:latest ${FRONTEND_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Push Images') {
            steps {
                sh """
                docker push ${BACKEND_REPO}:${IMAGE_TAG}
                docker push ${FRONTEND_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh """
                aws ecs update-service \
                  --cluster devops-challenge-cluster \
                  --service devops-challenge-backend-service \
                  --force-new-deployment \
                  --region ${AWS_REGION}

                aws ecs update-service \
                  --cluster devops-challenge-cluster \
                  --service devops-challenge-frontend-service \
                  --force-new-deployment \
                  --region ${AWS_REGION}
                """
            }
        }
    }
}
