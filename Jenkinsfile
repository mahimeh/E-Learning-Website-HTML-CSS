pipeline {
    agent any

    environment {
        IMAGE_NAME = "e-learning-site"
        DOCKERHUB_USER = "mahimeh"
    }

    stages {
        stage('Checkout Code') {
            steps {
                // Explicitly clean and clone your repo
                sh 'rm -rf *'
                sh 'git clone https://github.com/mahimeh/E-Learning-Website-HTML-CSS.git .'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:latest ."
            }
        }

        stage('Tag & Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASS')]) {
                    sh """
                        echo $DH_PASS | docker login -u $DH_USER --password-stdin
                        docker tag ${IMAGE_NAME}:latest ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                        docker push ${DOCKERHUB_USER}/${IMAGE_NAME}:latest
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    kubectl delete -f k8s-deployment.yaml || true
                    kubectl apply -f k8s-deployment.yaml
                    kubectl apply -f k8s-service.yaml
                """
            }
        }
    }

    post {
        always {
            echo "Pipeline finished — Build #${BUILD_NUMBER}"
        }
    }
}
