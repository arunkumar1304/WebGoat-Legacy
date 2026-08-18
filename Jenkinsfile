pipeline {
    agent any

    environment {
        BUILD_VERSION = '1'
    }

    stages {
        stage('Verify Jenkins Environment') {
            steps {
                sh 'java -version'
                sh 'mvn -version'
            }
        }

        stage('Build WebGoat Legacy with Java 8') {
            steps {
                sh '''
                    docker run --rm \
                      -v "$WORKSPACE":/workspace \
                      -w /workspace \
                      maven:3.9.9-eclipse-temurin-8 \
                      mvn -B -Dmaven.test.failure.ignore=true clean package
                '''
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
