pipeline {
    agent any
    environment {
        DOCKER_HUB_REPO = "saigopi123456/tomcat"
        DOCKERHUB_CREDENTIALS = credentials('dockerhub')
        CONTAINER_NAME = "tomcatcontainer"
        http_proxy = 'http://127.0.0.1:3128/'
        https_proxy = 'http://127.0.0.1:3128/'
        ftp_proxy = 'http://127.0.0.1:3128/'
        socks_proxy = 'socks://127.0.0.1:3128/'
    }
    stages {
        stage ('Docker Image Build') {
            steps {
                sh 'docker build -t  $DOCKER_HUB_REPO:$BUILD_NUMBER .'
            }
        }
        stage ('Stop & Delete Previous Containers') {
            steps {
                sh 'docker stop $(docker ps -a -q) || true && docker rm $(docker ps -a -q) || true'       
            }
        }
        stage ('create container') {
            steps {
                sh 'docker run -d --name $CONTAINER_NAME$BUILD_NUMBER -p 9028:8080 --restart unless-stopped $DOCKER_HUB_REPO:$BUILD_NUMBER && docker ps'
            }
        }
        stage ('Container Testing ') {
            steps {
                sh 'wget localhost:9028/gameoflife/'
            }
        }
        
        stage ('DockerHub Login and push') {
            steps {
                sh 'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR  --password-stdin && docker push $DOCKER_HUB_REPO:$BUILD_NUMBER'
            }
        }
        stage ('remove unused images') {
            steps {
                sh 'docker rmi -f $(docker images -a -q) || true'
            }
        }
    }
}
