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

podTemplate(label: label, cloud: 'kubernetes',
    containers: [],
    volumes: [
        hostPathVolume(mountPath: '/root/.gitconfig', hostPath: '/root/.gitconfig')
    ]
) {
    node(label) {
        stage('PingCode Flow CI/CD') {
            script {
                runCIStep();
            }
        }
    }
 }
