pipeline {
    agent any

    stages {
        stage('Deploy to K8') {
            steps {
                withCredentials([file(credentialsId: 'k8-ca-cert', variable: 'CA_CERT_FILE'), string(credentialsId: 'k8-token-mission', variable: 'K8_TOKEN')]) {
                    script {
                        sh '''
                            export KUBECONFIG=$(mktemp)
                            kubectl config set-cluster kubernetes --certificate-authority=$CA_CERT_FILE --server=https://kubernetes.docker.internal:6443
                            kubectl config set-credentials admin --token=$K8_TOKEN
                            kubectl config set-context default --cluster=kubernetes --user=admin
                            kubectl config use-context default
                            kubectl apply -f deployment-service.yml -n webapps
                            sleep 60
                        '''
                    }
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                withCredentials([file(credentialsId: 'k8-ca-cert', variable: 'CA_CERT_FILE'), string(credentialsId: 'k8-token-mission', variable: 'K8_TOKEN')]) {
                    script {
                        sh '''
                            export KUBECONFIG=$(mktemp)
                            kubectl config set-cluster kubernetes --certificate-authority=$CA_CERT_FILE --server=https://kubernetes.docker.internal:6443
                            kubectl config set-credentials admin --token=$K8_TOKEN
                            kubectl config set-context default --cluster=kubernetes --user=admin
                            kubectl config use-context default
                            kubectl get pods -n webapps
                            kubectl get svc -n webapps
                        '''
                    }
                }
            }
        }
    
    }
}
