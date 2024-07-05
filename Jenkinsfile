pipeline {
    agent any

    stages {
        stage('Checking the Config') {
            steps {
                sh "eksctl create cluster --config-file=cluster.yml --dry-run"
            }
        }
        stage('Creating Cluster') {
            steps {
                script {
                    def clusterExists = sh(script: 'aws eks describe-cluster --cluster-name realtime-project --region us-east-1', returnStatus: true) == 0
                    if (clusterExists){
                        echo "Cluster Already Exists"  
                    } else {
                        echo "Creating Cluster"
                        sh "eksctl create cluster --config-file=cluster.yml"
                    }
                }
            }
        }
        stage('Update Kube-config') {
            steps {
                sh "aws eks update-kubeconfig --name realtime-project --region us-east-1"
            }
        }
        stage('Apply ClusterRole and ClusterRoleBinding') {
            steps {
                sh 'kubectl apply -f cluster-role.yml'
            }
        }
        stage('Update AWS AUTH Config') {
            steps {
                sh 'kubectl patch configmap aws-auth -n kube-system --patch "$(cat aws-auth-cm.yml)"'
            }
        }
    }
}
