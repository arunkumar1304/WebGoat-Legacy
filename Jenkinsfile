pipeline {
    agent any

    environment {
        BUILD_VERSION = '6.0.1'
        ARTEFACT_NAME = "${WORKSPACE}/target/WebGoat-${BUILD_VERSION}.war"
        BUILD_TAG_NAME = "webgoat-${BUILD_VERSION}"
    }

    stages {

        stage('Build') {
            environment {
                JAVA_HOME = '/opt/java8'
                PATH = "/opt/java8/bin:${env.PATH}"
            }

            steps {
                sh 'java -version'
                sh 'mvn -version'

                sh '''
                    mvn -B \
                      -Dproject.version=$BUILD_VERSION \
                      -Dmaven.test.failure.ignore=true \
                      clean package
                '''
            }

            post {
                success {
                    echo 'Now archiving...'
                    archiveArtifacts artifacts: '**/target/*.war',
                                     fingerprint: true
                }
            }
        }

        stage('Nexus IQ Scan') {
            steps {
                script {
                    nexusPolicyEvaluation(
                        advancedProperties: '',
                        enableDebugLogging: false,
                        failBuildOnNetworkError: false,
                        failBuildOnScanningErrors: false,
                        iqApplication: selectedApplication('webgoat_legacy'),
                        iqInstanceId: 'nxiq',
                        iqScanPatterns: [
                            [scanPattern: '**/target/*.war']
                        ],
                        iqStage: 'build',
                        reachability: [
                            javaAnalysis: [
                                enable: true
                            ]
                        ]
                    )
                }
            }
        }

        stage('Publish to Repo') {
            steps {
                script {
                    nexusPublisher(
                        nexusInstanceId: 'nxrm3',
                        nexusRepositoryId: 'maven-releases',

                        packages: [[
                            $class: 'MavenPackage',

                            mavenAssetList: [[
                                classifier: '',
                                extension: 'war',
                                filePath: "${env.ARTEFACT_NAME}"
                            ]],

                            mavenCoordinate: [
                                artifactId: 'webgoat-legacy',
                                groupId: 'org.owasp.webgoat',
                                packaging: 'war',
                                version: "${env.BUILD_VERSION}"
                            ]
                        ]],

                        tagName: "${env.BUILD_TAG_NAME}"
                    )
                }
            }
        }
    }

    post {
        success {
            echo 'Build, IQ scan and Nexus publish completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }
    }
}
