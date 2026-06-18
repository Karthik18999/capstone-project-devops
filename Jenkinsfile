pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "karthikchitikela25/devops-app"
        PATH = "/var/lib/jenkins/bin:/usr/bin:/usr/local/bin:${env.PATH}"
    }

    stages {
        stage('Build JAR') {
            steps {
                dir('app') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $DOCKER_IMAGE:latest .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh '''
                    echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                    docker push $DOCKER_IMAGE:latest
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh "/var/lib/jenkins/bin kubectl apply -f k8s/green-deployment.yaml"
            }
        }
    }
}
