def label = "worker-${UUID.randomUUID().toString()}"

def setFailure() {
    currentBuild.result = "FAILURE"
}

def runCIStep() {
    def summaryMessage = "integration-test passed"

    try {
        def scmVars = checkout scm
        def branch = scmVars.GIT_BRANCH
        echo "${branch}"
        sh "git version"
        sh "git tag"
        sh "printenv"
        sh "git log"
    }
    catch(e) {
        summaryMessage = e.message
        setFailure()
    }
    finally {
        pingcodeBuildRecord(
            failOnError: true,
            overviewPattern: "^.*passing",
            defaultSummary: "${summaryMessage}"
        )
    }
}

pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                runCIStep();
            }
        }
    }
}
