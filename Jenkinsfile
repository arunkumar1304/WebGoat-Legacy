pipeline {
    agent any

    environment {
        BUILD_VERSION = '1'
    }

    stages {
        stage('Verify Environment') {
            steps {
                sh 'java -version'
                sh 'mvn -version'
            }
        }

        stage('Build WebGoat Legacy') {
            steps {
                sh 'mvn -B -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('Archive WAR') {
            steps {
                archiveArtifacts artifacts: '**/target/*.war',
                                 fingerprint: true
            }
        }
    }

    post {
        success {
            echo 'WebGoat Legacy build completed successfully.'
        }

        failure {
            echo 'Build failed. Check the console output.'
        }
    }
}
