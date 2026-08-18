stage('Sonatype Lifecycle Scan') {
    steps {
        script {
            def policyEvaluation = nexusPolicyEvaluation(
                iqApplication: selectedApplication('Webgoat'),
                iqInstanceId: 'nxiq',
                iqStage: 'build',
                iqScanPatterns: [
                    [scanPattern: '**/target/*.war']
                ],
                failBuildOnNetworkError: true,
                failBuildOnScanningErrors: true
            )

            echo "IQ Report: ${policyEvaluation.applicationCompositionReportUrl}"
        }
    }
}
