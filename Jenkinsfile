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

        stage('Build WebGoat Legacy') {
            environment {
                JAVA_HOME = '/opt/java8'
                PATH = "/opt/java8/bin:${env.PATH}"
            }

            steps {
                sh 'java -version'
                sh 'mvn -version'

                sh 'mvn -B -Dproject.version=$BUILD_VERSION -Dmaven.test.failure.ignore=true clean package'
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
