pipeline {
    agent any

    environment {
        DOCKERHUB_USER = 'jatink9599'
        IMAGE_NAME = 'springboot-app'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/jatinkapoor009/CI-CD-java-maven-docker.git'
            }
        }

        stage('Build with Maven') {
            steps {
                // ✅ Fix permission issue before running mvnw
                sh 'chmod +x mvnw'
                sh './mvnw clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $DOCKERHUB_USER/$IMAGE_NAME:${BUILD_NUMBER} ."
            }
        }

        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                    sh "docker push $DOCKERHUB_USER/$IMAGE_NAME:${BUILD_NUMBER}"
                }
            }
        }

        stage('Deploy Container') {
            steps {
                sh "docker stop springboot-app || true && docker rm springboot-app || true"
                sh "docker run -d --name springboot-app -p 8080:8080 $DOCKERHUB_USER/$IMAGE_NAME:${BUILD_NUMBER}"
            }
        }
    }
}
