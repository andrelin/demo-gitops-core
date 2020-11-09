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
            label "rdpmon-dev"
            serviceAccount "jenkins-build"
            yamlFile "helmsman.yaml"
        }
    }
    environment {
        cmAddr = "http://core-chartmuseum.core.svc.cluster.local:8080"
    }
    stages {
        stage("Deploy Dev") {
            when {
                branch "master"
            }
            steps {
                container("helmsman") {
                    withCredentials([
                        usernamePassword(
                            credentialsId: "chartmuseum",
                            usernameVariable: "CHARTMUSEUM_USER",
                            passwordVariable: "CHARTMUSEUM_PASSWORD"
                        ),
                        [$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'ecr-creds']
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
