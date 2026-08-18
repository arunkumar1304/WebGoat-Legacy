pipeline {
    agent any

    environment {
        BUILD_VERSION = '1'
        IQ_SCAN_URL = ''
    }

    stages {
        stage('Build') {
            steps {
                sh 'chmod +x mvnw || true'
                sh './mvnw -B -Dproject.version=$BUILD_VERSION -Dmaven.test.failure.ignore=true clean package'
            }
        }
    }
}
