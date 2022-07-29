pipeline { 
    environment {
        registry = "quangnghi/student-app-client" 
        registryCredential = 'dockerhub' 
        dockerImage = '' 
    }
    agent any 
    stages { 
        stage('Cloning Git') { 
            steps { 
                git 'https://github.com/quangnghi99/kubernetes-full-stack-example.git' 
            }
        } 
        stage('Building image') { 
            steps { 
                dir('react-student-management-web-app') {
                    script { 
                    dockerImage = docker.build registry + ":04" 
                    }
                }
                dir('spring-boot-student-app-api') {
                    sh 'mvn install'
                }
            }
        } 
        stage('Deploy image') { 
            steps { 
                script { 
                    docker.withRegistry( '', registryCredential ) { 
                        dockerImage.push() 
                    }
                }
                dir('spring-boot-student-app-api') {
                    sh 'mvn dockerfile:push'
                } 
            }
        }
        stage('Istio'){
            steps{
                sh 'helm repo add istio https://istio-release.storage.googleapis.com/charts'
                sh 'helm repo update'
                sh 'helm upgrade istio-base istio/base -n istio-system --install'
                sh 'helm upgrade istiod istio/istiod -n istio-system --wait --install'
            }
        }
        stage('Helm'){
            steps{
                sh 'helm upgrade nghi k8s --install'
            }
        }
        stage('Grafana and Prometheus'){
            steps{
                sh 'helm repo add bitnami https://charts.bitnami.com/bitnami'
                sh 'helm upgrade grafana bitnami/grafana --install'
                sh 'echo "Password: $(kubectl get secret grafana-admin --namespace default -o jsonpath="{.data.GF_SECURITY_ADMIN_PASSWORD}" | base64 -d)"'
                sh 'helm repo add prometheus-community https://prometheus-community.github.io/helm-charts'
                sh 'helm upgrade prometheus prometheus-community/prometheus --install'
            }
        }    
    }
}
