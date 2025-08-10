pipeline {
    agent any

    environment {
        DOCKER_BUILDKIT     = '0'
        DOCKERHUB_CREDS     = 'amol-docker'
        GIT_REPO            = 'https://github.com/amolkhetan/learner-report-cs/'
        BACKEND_REPO        = 'https://github.com/amolkhetan/learner-report-cs/'
        HELM_REPO           = 'https://github.com/amolkhetan/learner-report-cs/'
        K8S_NAMESPACE       = 'learner-report'
        AWS_REGION          = 'us-west-2'
        EKS_CLUSTER_NAME    = 'learnerreport'
    }

    stages {

        stage('Clone App Repository') {
            steps {
                git url: "${GIT_REPO}", branch: 'main'
            }
        }

        stage('Build & Push Docker Images') {
            steps {
                script {
                    def services = ['learnerReportCS_backend', 'learnerReportCS_frontend']
                    withCredentials([usernamePassword(
                        credentialsId: "${DOCKERHUB_CREDS}",
                        usernameVariable: 'DOCKERHUB_USER',
                        passwordVariable: 'DOCKERHUB_PASS'
                    )]) {
                        services.each { svc ->
                            def image = "${DOCKERHUB_USER}/learnerreportcs_${svc}".toLowerCase()
                            dir("./${svc}") {
                                sh """
                                    echo "🔧 Building image for ${image}"
                                    DOCKER_BUILDKIT=0 docker build --no-cache -t ${image}:latest .
                                    echo "${DOCKERHUB_PASS}" | docker login -u "${DOCKERHUB_USER}" --password-stdin
                                    docker push ${image}:latest
                                """
                            }
                        }
                    }
                }
            }
        }

        stage('Verify/Create EKS Cluster') {
            steps {
                withCredentials([[$class: 'AmazonWebServicesCredentialsBinding', credentialsId: 'amol-aws']]) {
                    script {
                        def clusterExists = sh(
                            script: "aws eks describe-cluster --name ${EKS_CLUSTER_NAME} --region ${AWS_REGION} --query 'cluster.status' --output text || echo 'NOT_FOUND'",
                            returnStdout: true
                        ).trim()

                        if (clusterExists == 'NOT_FOUND') {
                            echo "🔨 Cluster not found. Creating EKS cluster..."
                            sh """
                                eksctl create cluster \
                                    --name ${EKS_CLUSTER_NAME} \
                                    --region ${AWS_REGION} \
                                    --nodes 2 \
                                    --node-type t3.medium \
                                    --managed
                            """
                        } else {
                            echo "✅ Cluster '${EKS_CLUSTER_NAME}' already exists."
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

        stage('Deploy with Helm') {
            steps {
                sh """
                    echo "🚀 Deploying MERN stack using Helm"
                    helm lint helmchart/learner-reportcs-charts
                    helm upgrade --install learner-report helmchart/learner-reportcs-charts \
                        --namespace ${K8S_NAMESPACE} --create-namespace \
                        --set frontend.tag=latest \
                        --set backend.tag=latest
                    kubectl get pods -n ${K8S_NAMESPACE}
                """
            }
        }
    }

    post {
        success {
            echo "✅ MERN app deployed successfully to EKS!"
            emailext subject: "✅ Jenkins Job SUCCESS: MERN App Deployment",
                     body: "The Jenkins job completed successfully and deployed the MERN app to EKS.",
                     to: "amol.khetan@gmail.com"
        }
        failure {
            echo "❌ Deployment failed. Check logs for details."
            emailext subject: "❌ Jenkins Job FAILURE: MERN App Deployment",
                     body: "The Jenkins job failed during MERN app deployment. Please check the logs.",
                     to: "amol.khetan@gmail.com"
        }
    }
}
