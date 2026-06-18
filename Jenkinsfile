pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "karthikchitikela25/devops-app"
        // Explicitly inject the custom binary path into the Jenkins pipeline environment
        PATH = "/var/lib/jenkins/bin:/usr/bin:/usr/local/bin:${env.PATH}"
    }

    stages {
        stage('Build JAR') {
            steps {
                // Using dir() isolates the directory switch cleanly so it doesn't leak
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
                // With PATH defined in environment, you can now use standard clean syntax safely
                sh "kubectl apply -f k8s/green-deployment.yaml"
            }
        }
    }
}}
