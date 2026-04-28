pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "swedaelangovan2802/my-app"
    }

    stages {

        stage('Build') {
            steps {
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Deploy Dev') {
            when {
                expression { env.GIT_BRANCH == 'origin/dev' }
            }
            steps {
                sh '''
                docker rm -f test-container || true
                docker run -d -p 8081:80 --name test-container swedaelangovan2802/my-app
                '''
            }
        }

        stage('Deploy Prod') {
            when {
                expression { env.GIT_BRANCH == 'origin/main' }
            }
            steps {
                sh '''
                docker rm -f prod-container || true
                docker run -d -p 8082:80 --name prod-container swedaelangovan2802/my-app
                '''
            }
        }
    }
    }
