pipeline {
    agent any

    stages {

        stage('Build Backend Image') {
            steps {
                sh '''
                docker rmi -f backend-app || true
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true
                docker network create lab-net || true

                docker run -d --name backend1 --network lab-net backend-app
                docker run -d --name backend2 --network lab-net backend-app
                '''
            }
        }

        stage('Deploy NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker run -d \
                --name nginx-lb \
                --network lab-net \
                -p 80:80 \
                -v $(pwd)/nginx/default.conf:/etc/nginx/conf.d/default.conf \
                nginx
                '''
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Check console logs for errors.'
        }
    }
}

