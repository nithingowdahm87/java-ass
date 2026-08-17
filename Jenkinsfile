pipeline {
    agent any

    environment {
        IMAGE_NAME = "intelliclouds-app"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    tools {
        maven 'Maven-3.9'
        jdk   'JDK-17'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
            }
        }

        stage('Docker Run') {
            steps {
                sh "docker rm -f ${IMAGE_NAME} || true"
                sh """
                    CONTAINER=\$(docker ps -q --filter publish=8080)
                    if [ -n "\$CONTAINER" ]; then
                        docker rm -f \$CONTAINER
                    fi
                """
                sh "docker run -d --name ${IMAGE_NAME} -p 8080:8080 ${IMAGE_NAME}:${IMAGE_TAG}"
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
