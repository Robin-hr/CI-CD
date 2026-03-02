pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "hebrusrobin/cicd-app"
    }

    stages {
        stage('Clone') {
            steps {
                git branch: 'main', url: 'https://github.com/Robin-hr/yourrepo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Docker Build') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Push Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds',
                usernameVariable: 'USER',
                passwordVariable: 'PASS')]) {
                    sh '''
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy') {
            steps {
                sh '''
                ssh ubuntu@44.223.91.245 "
                docker pull $DOCKER_IMAGE:latest &&
                docker stop app || true &&
                docker rm app || true &&
                docker run -d -p 80:5000 --name app $DOCKER_IMAGE:latest
                "
                '''
            }
        }
    }
}