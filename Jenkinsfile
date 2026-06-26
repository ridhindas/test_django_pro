pipeline {
    agent any

    environment {
        IMAGE_NAME = "django-app"
        CONTAINER_NAME = "django-container"
        PORT = "8000"
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
                sh 'docker build -t $IMAGE_NAME .'
            }
        }

        stage('Django Check') {
            steps {
                sh 'docker run --rm $IMAGE_NAME python manage.py check'
            }
        }

        stage('Run Migrations') {
            steps {
                sh 'docker run --rm $IMAGE_NAME python manage.py migrate'
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
                  $IMAGE_NAME
                '''
            }
        }
    }

    post {
        success {
            echo "Deployment completed successfully."
        }

        failure {
            echo "Pipeline failed."
        }
    }
}
