pipeline {
    agent any

    environment {
        IMAGE_NAME = "ashcreatingdocker/flask-cicd-app"
        IMAGE_TAG = "v1"
    }

    stages {

        stage('Checkout Code') {
            steps {
                echo 'Code pulled successfully'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Image') {
            steps {
                sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
            }
        }

        stage('Deploy to EKS') {
    steps {
        sh '''
        aws eks update-kubeconfig --region ap-south-1 --name flask-cicd-cluster
        kubectl apply -f deployment.yaml
        kubectl apply -f service.yaml
        kubectl rollout status deployment/flask-app
        '''
    }
}
}
