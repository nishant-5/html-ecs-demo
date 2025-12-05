pipeline {
    agent any

    environment {
        AWS_REGION  = "us-east-1"
        ECR_REPO    = "656989657994.dkr.ecr.us-east-1.amazonaws.com/html-demo"
        ECS_CLUSTER = "html-cluster-demo"
        ECS_SERVICE = "html-demo-task-service"
        IMAGE_TAG   = "latest"
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                  docker build -t ${ECR_REPO}:${IMAGE_TAG} .
                """
            }
        }

        stage('Login & Push to ECR') {
            steps {
                sh """
                  aws ecr get-login-password --region ${AWS_REGION} \
                    | docker login --username AWS --password-stdin ${ECR_REPO}

                  docker push ${ECR_REPO}:${IMAGE_TAG}
                """
            }
        }

        stage('Deploy to ECS') {
            steps {
                sh """
                  aws ecs update-service \
                    --cluster ${ECS_CLUSTER} \
                    --service ${ECS_SERVICE} \
                    --force-new-deployment \
                    --region ${AWS_REGION}
                """
            }
        }
    }
}
