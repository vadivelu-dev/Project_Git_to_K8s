pipeline {

    agent any

    environment {
        IMAGE_NAME = "vadivelu123/parking_app"
    }

    stages {

        stage('Clone') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:latest .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([
                  usernamePassword(
                  credentialsId: 'dockerhub-creds',
                  usernameVariable: 'USER',
                  passwordVariable: 'PASS')
                ]) {

                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $IMAGE_NAME:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {

                withCredentials([
                  file(
                  credentialsId: 'kubeconfig',
                  variable: 'KUBECONFIG')
                ]) {

                    sh '''
                    kubectl apply -f deployment.yaml
                    '''
                }
            }
        }
    }
}
