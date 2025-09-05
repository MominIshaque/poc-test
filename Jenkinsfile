pipeline {
    agent any

    environment {
        AWS_ACCOUNT_ID   = "905418253962"
        AWS_REGION       = "us-west-2"
        ECR_REPO_NAME    = "myapp"
        IMAGE_REPO       = "${AWS_ACCOUNT_ID}.dkr.ecr.${AWS_REGION}.amazonaws.com/${ECR_REPO_NAME}"
        IMAGE_TAG        = "${BUILD_NUMBER}"
        K8S_NAMESPACE    = "default"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', url: 'https://github.com/MominIshaque/poc-test.git'
            }
        }

        stage('Login to ECR') {
            steps {
                sh '''
                  aws ecr get-login-password --region $AWS_REGION \
                  | docker login --username AWS --password-stdin $AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                sh '''
                  docker build -t $IMAGE_REPO:$IMAGE_TAG .
                '''
            }
        }

        stage('Push Docker Image to ECR') {
            steps {
                sh '''
                  docker push $IMAGE_REPO:$IMAGE_TAG
                '''
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh '''
                  # Replace placeholder <IMAGE> in deployment.yml with actual image
                  sed -i "s|<IMAGE>|$IMAGE_REPO:$IMAGE_TAG|g" k8s/deployment.yml

                  # Apply to Kubernetes
                  kubectl apply -n $K8S_NAMESPACE -f k8s/deployment.yml

                  # Verify rollout
                  kubectl rollout status deployment/myapp-deployment -n $K8S_NAMESPACE
                '''
            }
        }
    }

    post {
        success {
            echo "✅ Deployment successful! Image: $IMAGE_REPO:$IMAGE_TAG"
        }
        failure {
            echo "❌ Deployment failed. Check logs."
        }
    }
}
