// Deploys the packaged site to a host defined in Jenkins under
// "Publish over SSH". Host credentials are managed there, not in this file.
def deployTo(String serverName) {
    sshPublisher(
        failOnError: true,
        publishers: [
            sshPublisherDesc(
                configName: serverName,
                transfers: [
                    sshTransfer(
                        sourceFiles: 'src/**',
                        removePrefix: 'src/'
                    )
                ]
            )
        ]
    )
}

pipeline {
    agent any

    options {
        timestamps()
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    stages {
        stage('Build') {
            steps {
                sh './gradlew build'
                archiveArtifacts artifacts: 'build/distributions/*.zip', fingerprint: true
            }
        }

        stage('Deploy to staging') {
            when { branch 'master' }
            steps {
                deployTo('staging')
            }
        }

        stage('Deploy to production') {
            when { branch 'master' }
            steps {
                input message: 'Does the staging environment look OK?'
                milestone(1)
                deployTo('production')
            }
        }
    }
}
