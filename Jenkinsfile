pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'vikasjoshi11/k8s-scheduler-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Vikas1147/Scheduler.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
            docker build -t %DOCKER_IMAGE%:%BUILD_NUMBER% .
            docker tag %DOCKER_IMAGE%:%BUILD_NUMBER% %DOCKER_IMAGE%:latest
            '''
            }
        }

        stage('Push Docker Image') {
            steps {
                bat '''
            docker push %DOCKER_IMAGE%:%BUILD_NUMBER%
            docker push %DOCKER_IMAGE%:latest
            '''
            }
        }

        stage('Deploy CronJob') {
            steps {
                bat '''
            kubectl apply -f cronjob.yaml
            '''
            }
        }

        stage('Verify') {
            steps {
                bat '''
            kubectl get cronjobs
            kubectl get jobs
            '''
            }
        }
    }
}
