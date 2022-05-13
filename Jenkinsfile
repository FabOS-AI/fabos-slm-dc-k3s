def scenarios = [
    "master" : [
        "ubuntu2004": [
            ["setup",   "install-ubuntu2004"],
            ["setup",   "scaleup-ubuntu2004"],
            ["setup",   "scaledown-ubuntu2004"],
            ["use",     "deploy-ubuntu2004"],
            ["use",     "undeploy-ubuntu2004"],
            ["setup",   "uninstall-ubuntu2004"],
        ],
        "ubuntu2110" : [
            ["setup",   "install-ubuntu2110"],
            ["setup",   "scaleup-ubuntu2110"],
            ["setup",   "scaledown-ubuntu2110"],
            ["use",     "deploy-ubuntu2110"],
            ["use",     "undeploy-ubuntu2110"],
            ["setup",   "uninstall-ubuntu2110"],
        ],
        "ubuntu2204" : [
            ["setup",   "install-ubuntu2204"],
            ["setup",   "scaleup-ubuntu2204"],
            ["setup",   "scaledown-ubuntu2204"],
            ["use",     "deploy-ubuntu2204"],
            ["use",     "undeploy-ubuntu2204"],
            ["setup",   "uninstall-ubuntu2204"],
        ],
    ],
    "Agent-1" : [
        "centos7" : [
            ["setup",   "install-centos7"],
            ["setup",   "scaleup-centos7"],
            ["setup",   "scaledown-centos7"],
            ["use",     "deploy-centos7"],
            ["use",     "undeploy-centos7"],
            ["setup",   "uninstall-centos7"],
        ],
        "centos8" : [
            ["setup",   "install-centos8"],
            ["setup",   "scaleup-centos8"],
            ["setup",   "scaledown-centos8"],
            ["use",     "deploy-centos8"],
            ["use",     "undeploy-centos8"],
            ["setup",   "uninstall-centos8"],
        ],
        "ubuntu1804" : [
            ["setup",   "install-ubuntu1804"],
            ["setup",   "scaleup-ubuntu1804"],
            ["setup",   "scaledown-ubuntu1804"],
            ["use",     "deploy-ubuntu1804"],
            ["use",     "undeploy-ubuntu1804"],
            ["setup",   "uninstall-ubuntu1804"],
        ],
    ],
]

def nodes = [:]

for (kv in mapToList(scenarios)) {
    def nodeName = kv[0]
    def distList = kv[1]

    nodes[nodeName] = {
        node("${nodeName}") {

            /**
            *
            *   CHECKOUT
            *
            **/

            stage("Checkout") {
                checkout scm

                sh 'ansible-galaxy install -f -r requirements.yml'
            }

            for (xy in mapToList(distList)) {
                def distName = xy[0]
                def scenarioList = xy[1]

                try {
                    /**
                    *
                    *   CREATE
                    *
                    **/

                    def createRole = scenarioList.first()[0]
                    def createScenario = scenarioList.first()[1]

                    stage("Create - ${distName}") {
                        try {
                            withEnv([
                                    'HYPERVISOR_ANSIBLE_USER=',
                                    'HYPERVISOR_ANSIBLE_HOST=localhost',
                                    'HYPERVISOR_ANSIBLE_CONNECTION=local',
                                    'HYPERVISOR_ANSIBLE_PASSWORD=',
                                    'ANSIBLE_SERIAL=5',
                            ]) {
                                sh "cd ./roles/${createRole} && molecule reset -s ${createScenario} && molecule create -s ${createScenario}"
                            }
                        } catch(crtErr) {
                            echo "Caught: ${crtErr}"
                            retry(2) {
                                withEnv([
                                        'HYPERVISOR_ANSIBLE_USER=',
                                        'HYPERVISOR_ANSIBLE_HOST=localhost',
                                        'HYPERVISOR_ANSIBLE_CONNECTION=local',
                                        'HYPERVISOR_ANSIBLE_PASSWORD=',
                                        'ANSIBLE_SERIAL=5',
                                ]) {
                                    sh "cd ./roles/${createRole} && molecule destroy -s ${createScenario}"
                                    sh "cd ./roles/${createRole} && molecule create -s ${createScenario}"
                                }
                            }
                        }
                    }

                    /**
                    *
                    *   INSTALL, SCALEUP, SCALEDOWN, DEPLOY, UNDEPLOY, UNINSTALL
                    *
                    **/

                    for(int i = 0; i < scenarioList.size(); i++) {
                        def role = scenarioList[i][0]
                        def scenario = scenarioList[i][1]
                        stage("${scenario}") {
                            withEnv([
                            'HYPERVISOR_ANSIBLE_USER=',
                            'HYPERVISOR_ANSIBLE_HOST=localhost',
                            'HYPERVISOR_ANSIBLE_CONNECTION=local',
                            'HYPERVISOR_ANSIBLE_PASSWORD=',
                            'ANSIBLE_SERIAL=5',
                            ]) {
                                retry(10) {
                                    sh "cd ./roles/${role} && molecule converge -s ${scenario} && molecule verify -s ${scenario}"
                                }
                            }
                        }
                    }
                } catch(err) {
                    currentBuild.result = "UNSTABLE"
                    echo "${distName} failed."
                    echo "Caught: ${err}"
                }  finally {
                    /**
                    *
                    *   DESTROY
                    *
                    **/

                    def destroyRole = scenarioList.first()[0]
                    def destroyScenario = scenarioList.first()[1]

                    stage("Destroy - ${distName}") {
                        withEnv([
                        'HYPERVISOR_ANSIBLE_USER=',
                        'HYPERVISOR_ANSIBLE_HOST=localhost',
                        'HYPERVISOR_ANSIBLE_CONNECTION=local',
                        'HYPERVISOR_ANSIBLE_PASSWORD=',
                        'ANSIBLE_SERIAL=5',
                        ]) {
                            retry(2) {
                                sh "cd ./roles/${destroyRole} && molecule destroy -s ${destroyScenario}"
                            }
                        }
                    }
                }
            }
        }
    }
}

@NonCPS
List<List<?>> mapToList(Map map) {
  return map.collect { it ->
    [it.key, it.value]
  }
}

parallel nodes