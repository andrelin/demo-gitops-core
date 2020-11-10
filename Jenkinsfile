u = new utils()
import java.text.SimpleDateFormat

pipeline {
    options {
        buildDiscarder logRotator(numToKeepStr: '5')
        disableConcurrentBuilds()
    }
    agent {
        kubernetes {
            cloud "kubernetes"
            label "core"
            serviceAccount "jenkins"
            yamlFile "demo-deployer.yaml"
        }
    }
    stages {
        stage("Deploy Dev") {
            when {
                branch "master"
            }
            steps {
                container("demo-deployer") {
                    withCredentials([
                        usernamePassword(
                            credentialsId: "chartmuseum",
                            usernameVariable: "CHARTMUSEUM_USER",
                            passwordVariable: "CHARTMUSEUM_PASSWORD"
                        ),
                    ]) {
                        script {
                            lock(u.get_lock_name("rdpmon", "rdp-monitoring-dev")) {
                                sh "helmsman --debug --apply -f dsf.toml --kubeconfig kubeconfig.yaml"
                            }
                        }
                    }
                }
            }
        }
    }
}
