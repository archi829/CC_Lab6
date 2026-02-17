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
            docker network create lab-net || true

            docker rm -f backend1 backend2 || true

            docker run -d --name backend1 --network lab-net backend-app
            docker run -d --name backend2 --network lab-net backend-app

            sleep 5
            '''
        }
}

stage('Start NGINX Load Balancer') {
    steps {
        sh '''
        docker rm -f nginx-lb || true

        docker run -d -p 8081:80 --name nginx-lb --network lab-net nginx

        sleep 5

        docker cp nginx/default.conf nginx-lb:/etc/nginx/conf.d/default.conf
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
