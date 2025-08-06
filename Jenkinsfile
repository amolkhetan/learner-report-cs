pipeline {
    agent any

    environment {
        DOCKER_BUILDKIT     = '0'
        DOCKERHUB_CREDS     = 'amol-docker'
        FRONTEND_REPO       = 'https://github.com/amolkhetan/learner-report-cs/tree/main/learnerReportCS_frontend'
        BACKEND_REPO        = 'https://github.com/amolkhetan/learner-report-cs/tree/main/learnerReportCS_backend'
        HELM_REPO           = 'https://github.com/amolkhetan/learner-report-cs/tree/main/learner-reportcs-charts'
        K8S_NAMESPACE       = 'learner-report'
        AWS_REGION          = 'us-west-2'
        EKS_CLUSTER_NAME    = 'peculiar-bluegrass-hideout'
    }

    stages {

        stage('Clone App Repositories') {
            steps {
                dir('frontend') {
                    git url: "${FRONTEND_REPO}", branch: 'main'
                }
                dir('backend') {
                    git url: "${BACKEND_REPO}", branch: 'main'
                }
            }
        }

        stage('Build & Push Docker Images') {
            steps {
                script {
                    def services = ['frontend', 'backend']
                    withCredentials([usernamePassword(
                        credentialsId: "${DOCKERHUB_CREDS}",
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )]) {
                        docker.withRegistry('https://index.docker.io/v1/', "${DOCKERHUB_CREDS}") {
                            services.each { svc ->
                                def image = "${DOCKERHUB_USER}/learnerreportcs_${svc}".toLowerCase()
                                dir("${svc}") {
                                    sh """
                                        echo "🔧 Building image for ${image}"
                                        DOCKER_BUILDKIT=0 docker build --no-cache -t ${image}:latest .
                                        docker push ${image}:latest
                                    """
                                }
                            }
                        }
                    }
                }
            }
        }

        stage('Configure KubeContext') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'amol-aws']]) {
                    sh """
                        echo "🔑 Configuring AWS and Kube context"
                        aws sts get-caller-identity
                        aws eks --region ${AWS_REGION} update-kubeconfig --name ${EKS_CLUSTER_NAME}
                        kubectl config current-context
                    """
                }
            }
        }

        stage('Prepare Helm Chart') {
            steps {
                dir('helmchart') {
                    git url: "${HELM_REPO}", branch: 'main'
                }
            }
        }

        stage('Deploy with Helm') {
            steps {
                sh """
                    echo "🚀 Deploying MERN stack using Helm"
                    helm lint helmchart/learner-reportcs-charts
                    helm upgrade --install learner-report helmchart/learner-reportcs-charts \\
                        --namespace ${K8S_NAMESPACE} --create-namespace \\
                        --set frontend.tag=latest \\
                        --set backend.tag=latest
                    kubectl get pods -n ${K8S_NAMESPACE}
                """
            }
        }
    }

    post {
        success {
            echo "✅ MERN app deployed successfully to EKS!"
        }
        failure {
            echo "❌ Deployment failed. Check logs for details."
        }
    }
}