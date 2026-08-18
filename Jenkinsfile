pipeline {
    agent any


    environment {
        BUILD_VERSION = '6.0.1'
        ARTEFACT_NAME = "${WORKSPACE}/target/WebGoat-${BUILD_VERSION}.war"
        IQ_SCAN_URL = ""
        BUILD_TAG = "webgoat-${BUILD_VERSION}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -Dproject.version=$BUILD_VERSION -Dmaven.test.failure.ignore clean package'
            }
            post {
                success {
                    echo 'Now archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage('Nexus IQ Scan') {
            steps {
                script {
                    def policyEvaluation = nexusPolicyEvaluation(
                        advancedProperties: '',
                        enableDebugLogging: false,
                        failBuildOnNetworkError: false,
                        failBuildOnScanningErrors: false,
                        iqApplication: selectedApplication('webgoat_legacy'),
                        iqInstanceId: 'nxiq',
                        iqScanPatterns: [[scanPattern: '**/*.war']],
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
                                filePath: "${ARTEFACT_NAME}"
                            ]],
                            mavenCoordinate: [
                                artifactId: 'WebGoat',
                                groupId: 'org.demo',
                                packaging: 'war',
                                version: "${BUILD_VERSION}"
                            ]
                        ]],
                        tagName: "${BUILD_TAG}"
                    )
                }
            }
        }
    }
}