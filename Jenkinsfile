pipeline{
    agent any

    environment {
        DOCKER_IMAGE = 'vikasjoshi11/k8s-scheduler-demo'
        NAMESPACE = 'scheduler-demo'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Vikas1147/Scheduler.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                docker build -t ${DOCKER_IMAGE}:${BUILD_NUMBER} .
                docker tag ${DOCKER_IMAGE}:${BUILD_NUMBER} ${DOCKER_IMAGE}:latest
            """
            }
        }

        stage('Push Docker Image') {
            steps {
                sh """
                docker push ${DOCKER_IMAGE}:${BUILD_NUMBER}
                docker push ${DOCKER_IMAGE}:latest
            """
            }
        }

        stage('Create Namespace') {
            steps {
                sh """
                kubectl get namespace ${NAMESPACE} || kubectl create namespace ${NAMESPACE}
            """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                kubectl apply -n ${NAMESPACE} -f deployment.yaml

                if [ -f service.yaml ]; then
                    kubectl apply -n ${NAMESPACE} -f service.yaml
                fi

                kubectl rollout restart deployment/scheduler-demo -n ${NAMESPACE} || true
            """
            }
        }

        stage('Verify') {
            steps {
                sh """
                kubectl get pods -n ${NAMESPACE}
                kubectl get svc -n ${NAMESPACE}
            """
            }
        }
    }

    post {
        always {
            sh 'docker image prune -f || true'
        }
    }
}
