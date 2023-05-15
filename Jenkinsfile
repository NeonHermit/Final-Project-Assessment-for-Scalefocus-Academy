pipeline {
    agent any

    environment {
        KUBECONFIG = '/var/lib/jenkins/.kube/config'
    }

    stages {
        stage('Namespace') {
            steps {
                script {
                    def namespaceExists = sh(script: 'kubectl get namespace wp', returnStatus: true) == 0
                    if (namespaceExists) {
                        echo 'Namespace wp exists'
                        return
                    } else {
                        echo 'Creating wp namespace'
                        sh 'kubectl create namespace wp'
                    }
                }
            }
        }

        stage('Helm') {
            steps {
                script {
                    sh 'helm dependency build'
                    sh 'helm upgrade --install final-project-wp-scalefocus ./bitnami/wordpress -n wp'
                    sh 'kubectl port-forward --namespace wp svc/final-project-wp-scalefocus-wordpress 9000:80'
                }
            }
        }
    }
}
