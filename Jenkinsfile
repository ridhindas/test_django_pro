pipeline {
    agent any

    environment {
        DOCKERHUB_USER = "ridhindas"
        IMAGE_NAME = "${DOCKERHUB_USER}/django-app"
        CONTAINER_NAME = "django-container"
        PORT = "8000"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ridhindas/test_django_pro.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $IMAGE_NAME:$IMAGE_TAG ."
            }
        }

        stage('Django Check') {
            steps {
                sh "docker run --rm $IMAGE_NAME:$IMAGE_TAG python manage.py check"
            }
        }

        stage('Run Migrations') {
            steps {
                sh "docker run --rm $IMAGE_NAME:$IMAGE_TAG python manage.py migrate"
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                sh "docker push $IMAGE_NAME:$IMAGE_TAG"
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker stop $CONTAINER_NAME || true
                docker rm $CONTAINER_NAME || true

                docker run -d \
                  --name $CONTAINER_NAME \
                  -p $PORT:8000 \
                  --restart unless-stopped \
                  $IMAGE_NAME:$IMAGE_TAG
                '''
            }
        }
    }

    post {
        success {
            echo "CI/CD Pipeline completed successfully 🚀"
        }

        failure {
            echo "Pipeline failed ❌"
        }
    }
}
