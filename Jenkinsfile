pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "swedaelangovan/my-app"
        TAG = "${env.BUILD_NUMBER}"
    }

    stages {

        stage('Build') {
            steps {
                sh "docker build -t $DOCKER_IMAGE:$TAG ."
                sh "docker tag $DOCKER_IMAGE:$TAG $DOCKER_IMAGE:latest"
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'USER',
                    passwordVariable: 'PASS'
                )]) {
                    sh "echo $PASS | docker login -u $USER --password-stdin"
                }
            }
        }

        stage('Push') {
            steps {
                sh "docker push $DOCKER_IMAGE:$TAG"
                sh "docker push $DOCKER_IMAGE:latest"
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                docker rm -f prod-container || true
                docker run -d -p 8082:80 --name prod-container swedaelangovan/my-app:latest
                '''
            }
        }
    }
}