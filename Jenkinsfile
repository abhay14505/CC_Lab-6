pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                sh '''
                echo "Building backend image..."

                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Create Network') {
            steps {
                sh '''
                echo "Creating Docker network..."

                docker network create lab-net || true
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                echo "Deploying backend containers..."

                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 --network lab-net backend-app
                docker run -d --name backend2 --network lab-net backend-app
                '''
            }
        }

        stage('Build NGINX Image') {
            steps {
                sh '''
                echo "Building custom NGINX image..."

                docker rmi -f custom-nginx || true
                docker build -t custom-nginx nginx
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                echo "Starting NGINX load balancer..."

                docker rm -f nginx-lb || true

                docker run -d \
                --name nginx-lb \
                --network lab-net \
                -p 80:80 \
                custom-nginx
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed. Check console logs for errors.'
        }
    }
}


