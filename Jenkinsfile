pipeline {
    agent { label 'docker' }

    environment {
        DOCKERHUB = "admin2325"
        EC2_IP = "54.224.204.17"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/VasaviK23/cicd-java-app.git',
                    credentialsId: 'github-creds'
            }
        }

        stage('Build Docker Images') {
            steps {
                sh 'docker build -t $DOCKERHUB/frontend-app ./frontend'
                sh 'docker build -t $DOCKERHUB/backend-app ./backend'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-creds', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh 'echo $PASS | docker login -u $USER --password-stdin'
                }
            }
        }

        stage('Push Images') {
            steps {
                sh 'docker push $DOCKERHUB/frontend-app'
                sh 'docker push $DOCKERHUB/backend-app'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh']) {
                    sh '''
                    ssh -o StrictHostKeyChecking=no ec2-user@54.224.204.17 "
                    docker pull $DOCKERHUB/backend-app &&
                    docker pull $DOCKERHUB/frontend-app &&
                    docker-compose up -d
                    "
                    '''
                }
            }
        }
    }
}