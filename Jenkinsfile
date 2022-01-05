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
    containers: [
        containerTemplate(name: 'wtctl', image: 'registry.cn-beijing.aliyuncs.com/worktile/wtctl:m1.0.0', ttyEnabled: true, command: 'cat',resourceLimitCpu: '200m'),
        containerTemplate(name: 'node', image: 'registry.cn-beijing.aliyuncs.com/worktile/dind-helm-k8s-mongod:1.0.1', ttyEnabled: true, command: 'cat',resourceLimitCpu: '400m')
    ],
    volumes: [
        hostPathVolume(mountPath: '/var/run/docker.sock', hostPath: '/var/run/docker.sock'),
        hostPathVolume(mountPath: '/mongod', hostPath: '/data/cache/mongod'),
        hostPathVolume(mountPath: '/redis', hostPath: '/data/cache/redis-stable'),
        hostPathVolume(mountPath: '/root/.npm', hostPath: '/data/cache/.npm'),
        hostPathVolume(mountPath: '/tmp/cache', hostPath: '/data/cache/pc-flow-gaea'),
        hostPathVolume(mountPath: '/root/.docker', hostPath: '/root/.docker'),
        hostPathVolume(mountPath: '/tmp/worktile.sh', hostPath: '/data/cache/worktile.sh'),
        hostPathVolume(mountPath: '/home/jenkins/agent', hostPath: '/data/cache/code'),
        hostPathVolume(mountPath: '/root/.gitconfig', hostPath: '/root/.gitconfig'),
        hostPathVolume(mountPath: '/root/.ssh', hostPath: '/root/.ssh')
    ]
) {
    node(label) {
        stage('PingCode Flow CI/CD') {
            script {
                container('wtctl') {
                    runCIStep();
                }
            }
        }
    }
 }
