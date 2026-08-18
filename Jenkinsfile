pipeline {
    agent any

    environment {
        BUILD_VERSION = '1'
        IQ_SCAN_URL = ''
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
                sh 'mvn -B -Dmaven.test.failure.ignore=true clean package'
            }
        }

        stage('Archive WAR') {
            steps {
                archiveArtifacts artifacts: '**/target/*.war',
                                 fingerprint: true
            }
        }

        stage('Sonatype IQ Evaluation') {
            steps {
                script {
                    echo 'Starting Sonatype IQ evaluation...'

                    def policyEvaluation = nexusPolicyEvaluation(
                        iqApplication: selectedApplication('WebGoat-Legacy'),
                        iqInstanceId: 'nxiq',
                        iqStage: 'build',
                        iqScanPatterns: [
                            [scanPattern: '**/target/*.war']
                        ],
                        failBuildOnNetworkError: true,
                        failBuildOnScanningErrors: true
                    )

                    env.IQ_SCAN_URL =
                        policyEvaluation.applicationCompositionReportUrl

                    echo "IQ Report: ${env.IQ_SCAN_URL}"
                }
            }
        }
    }

    post {
        success {
            echo 'Build and IQ evaluation completed successfully.'
            echo "IQ Report: ${env.IQ_SCAN_URL}"
        }

        failure {
            echo 'Pipeline failed. Check Console Output.'
        }
    }
}
