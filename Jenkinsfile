pipeline {
    agent any

    stages {

        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Build Backend Image') {
            steps {
                sh '''
                docker build -t backend-app backend
                '''
            }
        }

        stage('Deploy Backend Containers') {
            steps {
                sh '''
                docker rm -f backend1 backend2 || true

                docker run -d --name backend1 backend-app
                docker run -d --name backend2 backend-app

                sleep 3
                '''
            }
        }

        stage('Start NGINX Load Balancer') {
            steps {
                sh '''
                docker rm -f nginx-lb || true

                docker run -d -p 80:80 --name nginx-lb nginx

                sleep 8

                docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
                sleep 3
                docker exec nginx-lb nginx -s reload
                '''
            }
        }
    }

    post {
        success {
            echo 'Deployment successful. Load balancer is running.'
        }
        failure {
            echo 'Pipeline failed. Check logs.'
        }
    }
}
