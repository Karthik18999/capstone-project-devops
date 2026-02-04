pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                sh 'cd app && mvn clean package'
            }
        }
    }
}
