@Library('itx-jenkins-shared-lib') _
// Find documentation online here:
// http://gitlab.kelle.grp/itx/jenkins-shared-lib/blob/master/README.md

pipeline {
    agent { label 'linux' }
    options {
        gitLabConnection('ithinx gitlab connection')
        gitlabCommitStatus(name: 'jenkins')
    }

    stages {
        stage('Clean') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh '''
                     cd recovery_image && ./build.sh
                   '''
            }
        }
        stage('Archive') {
            steps {
                // recovery.img
                archiveArtifacts artifacts: 'recovery.img', onlyIfSuccessful: true
            }
        }
    }
    post {
        always {
            // This method from the ithinx shared lib will trigger gitlab, slack, and email depending on result
            triggerNotificationsOnBuild(currentBuild.result, "")

            updateGitlabCommitStatus name: 'jenkins',
                                     state: 'SUCCESS' == currentBuild.result ? 'success' : 'failed'
        }
        success {
            steps {
                // cleanup workspace to reduce Jenkins footprint (keep sources for browsing)
                cleanWs()
            }
        }
    }
}

