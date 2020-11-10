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
        stage("Deploy Core") {
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
                            lock("deploy-core") {
                                sh "helmsman --debug --apply -f dsf.toml --kubeconfig kubeconfig.yaml"
                            }
                        }
                    }
                }
            }
        }
    }
}
