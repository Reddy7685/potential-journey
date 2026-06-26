stages {

    stage('Clean Up') {
        ...
    }

    stage('Set Up Network') {
        ...
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
            ...
        }
    }

    stage('Manual Check') {
        steps {
            ...
        }
    }
}
