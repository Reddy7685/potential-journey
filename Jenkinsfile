pipeline {
    agent any

    stages {
        stage('Clean Up') {
            steps {
                sh 'docker rm -f task1-app task1-nginx || true'
                sh 'docker network rm task1-network || true'
                sh 'docker rmi task1-app-image task1-nginx-image || true'
            }
        }

        stage('Set Up Network') {
            steps {
                sh 'docker network create task1-network'
            }
        }

        stage('Build Images') {
            steps {
                sh 'docker build -t task1-app-image -f Dockerfile.app .'
                sh 'docker build -t task1-nginx-image -f Dockerfile.nginx .'
            }
        }

        stage('Run Containers') {
            steps {
                sh 'docker run -d --name task1-app --network task1-network task1-app-image'
                sh 'docker run -d --name task1-nginx --network task1-network -p 80:80 task1-nginx-image'
            }
        }

        stage('Manual Check') {
            steps {
                sh 'docker ps'
                sh 'curl http://localhost:80'
            }
        }
    }

    post {
        always {
            echo 'Pipeline finished'
        }
    }
}
