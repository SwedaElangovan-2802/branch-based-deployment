pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "SwedaElangovan-2802/my-app"
        TAG = "${env.BRANCH_NAME}"
    }

    stages {

        stage('Clone') {
            steps {
                git branch: "${env.BRANCH_NAME}", url: 'https://github.com/SwedaElangovan-2802/branch-based-deployment.git'
            }
        }

        stage('Build') {
            steps {
                sh "docker build -t $DOCKER_IMAGE:$TAG ."
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
            }
        }

        stage('Deploy') {
            steps {
                script {
                    if (env.BRANCH_NAME == "dev") {
                        sh """
                        docker rm -f test-container || true
                        docker run -d -p 8081:80 --name test-container $DOCKER_IMAGE:dev
                        """
                    } else if (env.BRANCH_NAME == "main") {
                        sh """
                        docker rm -f prod-container || true
                        docker run -d -p 8082:80 --name prod-container $DOCKER_IMAGE:main
                        """
                    }
                }
            }
        }
    }
}