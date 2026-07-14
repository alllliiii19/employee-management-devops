pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                echo 'Source code will be checked from github.'
            }
    }

    stage('Build') {
        steps {
            echo 'Building the application.'
            sh 'mvn clean package'
        }
    }

    stage('SonarQube Analysis') {
    steps {
        withSonarQubeEnv('SonarQube') {
            sh '''
                mvn sonar:sonar \
                -Dsonar.projectKey=employee-management-devops
            '''
        }
      }
    }
    stage('Docker Build') {
    steps {
        sh '''
            docker build -t employee-management-webapp:v1 .
        '''
    }
   }
   stage('Push to Amazon ECR') {
    environment {
        AWS_ACCOUNT_ID = '869425948062'
        AWS_REGION = 'us-east-1'
        ECR_REPOSITORY = 'employee-magt-webapp'
    }

    steps {
        sh '''
            ECR_REGISTRY=$AWS_ACCOUNT_ID.dkr.ecr.$AWS_REGION.amazonaws.com

            aws ecr get-login-password --region $AWS_REGION | \
            docker login --username AWS --password-stdin $ECR_REGISTRY

            docker tag employee-management-webapp:v1 \
            $ECR_REGISTRY/$ECR_REPOSITORY:v1

            docker push \
            $ECR_REGISTRY/$ECR_REPOSITORY:v1
        '''
    }
   
   }
   stage('Deploy to Amazon ECS') {
    steps {
        sh '''
            aws ecs update-service \
                --cluster myapp \
                --service myappservice \
                --force-new-deployment \
                --region us-east-1
        '''
    }
   }
  } 
}