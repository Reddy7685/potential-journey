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

        stage('Trivy FS Scan') {
            steps {
                sh 'trivy fs -f json -o trivy-fs-report.json .'
                archiveArtifacts artifacts: 'trivy-fs-report.json', fingerprint: true
            }
        }

        stage('Trivy Image Scan') {
            steps {
                sh 'trivy image -f json -o trivy-image-report.json task1-app-image'
                archiveArtifacts artifacts: 'trivy-image-report.json', fingerprint: true
            }
        }

        stage('Run Containers') {
            steps {
                sh 'docker run -d --name task1-app --network task1-network task1-app-image'
                sh 'docker run -d --name task1-nginx --network task1-network -p 80:80 task1-nginx-image'
            }
        }

        stage('Unit Tests') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    sh '''
                        python3 -m venv venv
                        . venv/bin/activate
                        pip install requests
                        python3 test_app.py
                    '''
                }
            }
        }

        stage('Manual Check') {
            steps {
                sh 'docker ps'
                sh 'curl http://localhost'
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'trivy-*.json', allowEmptyArchive: true
            echo 'Pipeline Finished'
        }
    }
}
