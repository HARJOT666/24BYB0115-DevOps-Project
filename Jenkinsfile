// ===================================================
// Jenkinsfile - NexaCorp Corporate Website CI/CD Pipeline
// Declarative Pipeline Syntax
// ===================================================

pipeline {
    agent any

    environment {
        // Docker configuration
        DOCKER_REGISTRY    = 'docker.io'
        DOCKER_IMAGE       = 'nexacorp/corporate-website'
        DOCKER_TAG         = "${env.BUILD_NUMBER}"
        DOCKER_CREDENTIALS = 'docker-hub-credentials'

        // Kubernetes configuration
        KUBE_NAMESPACE     = 'nexacorp'
        KUBE_DEPLOYMENT    = 'corporate-website'
        KUBECONFIG_CRED    = 'kubeconfig-credentials'

        // Application configuration
        APP_NAME           = 'corporate-website'
        APP_PORT           = '80'
    }

    options {
        buildDiscarder(logRotator(numToKeepStr: '10'))
        timestamps()
        timeout(time: 30, unit: 'MINUTES')
        disableConcurrentBuilds()
    }

    stages {
        // ==================== Stage 1: Checkout ====================
        stage('Checkout') {
            steps {
                echo '========== Checking out source code =========='
                checkout scm
                echo "Branch: ${env.BRANCH_NAME}"
                echo "Build Number: ${env.BUILD_NUMBER}"
                echo "Commit: ${env.GIT_COMMIT}"
            }
        }

        // ==================== Stage 2: Maven Build ====================
        stage('Maven Build') {
            steps {
                echo '========== Running Maven Build =========='
                sh 'mvn clean package -DskipTests'
                echo 'Maven build completed successfully.'
            }
            post {
                success {
                    archiveArtifacts artifacts: 'target/*.war', fingerprint: true
                }
            }
        }

        // ==================== Stage 3: Docker Build ====================
        stage('Docker Build') {
            steps {
                echo '========== Building Docker Image =========='
                sh """
                    docker build -t ${DOCKER_IMAGE}:${DOCKER_TAG} .
                    docker build -t ${DOCKER_IMAGE}:latest .
                """
                echo "Docker image built: ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

        // ==================== Stage 4: Docker Push (Optional) ====================
        stage('Docker Push') {
            when {
                branch 'main'
            }
            steps {
                echo '========== Pushing Docker Image to Registry =========='
                withCredentials([usernamePassword(
                    credentialsId: "${DOCKER_CREDENTIALS}",
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    sh """
                        echo "\${DOCKER_PASS}" | docker login ${DOCKER_REGISTRY} -u "\${DOCKER_USER}" --password-stdin
                        docker push ${DOCKER_IMAGE}:${DOCKER_TAG}
                        docker push ${DOCKER_IMAGE}:latest
                        docker logout ${DOCKER_REGISTRY}
                    """
                }
                echo "Docker image pushed: ${DOCKER_IMAGE}:${DOCKER_TAG}"
            }
        }

        // ==================== Stage 5: Deploy to Kubernetes ====================
        stage('Deploy to Kubernetes') {
            when {
                branch 'main'
            }
            steps {
                echo '========== Deploying to Kubernetes =========='
                withCredentials([file(credentialsId: "${KUBECONFIG_CRED}", variable: 'KUBECONFIG')]) {
                    sh """
                        # Create namespace if it doesn't exist
                        kubectl create namespace ${KUBE_NAMESPACE} --dry-run=client -o yaml | kubectl apply -f -

                        # Apply Kubernetes manifests
                        kubectl apply -f k8s/deployment.yaml -n ${KUBE_NAMESPACE}
                        kubectl apply -f k8s/service.yaml -n ${KUBE_NAMESPACE}

                        # Update deployment image to the new build
                        kubectl set image deployment/${KUBE_DEPLOYMENT} \
                            ${KUBE_DEPLOYMENT}=${DOCKER_IMAGE}:${DOCKER_TAG} \
                            -n ${KUBE_NAMESPACE}

                        # Wait for rollout to complete
                        kubectl rollout status deployment/${KUBE_DEPLOYMENT} \
                            -n ${KUBE_NAMESPACE} --timeout=120s

                        echo "Deployment successful!"
                        kubectl get pods -n ${KUBE_NAMESPACE} -l app=${KUBE_DEPLOYMENT}
                        kubectl get svc -n ${KUBE_NAMESPACE} -l app=${KUBE_DEPLOYMENT}
                    """
                }
            }
        }
    }

    post {
        always {
            echo '========== Pipeline Completed =========='
            cleanWs()
        }
        success {
            echo '========== Pipeline SUCCESS =========='
            echo "Build ${env.BUILD_NUMBER} completed successfully."
        }
        failure {
            echo '========== Pipeline FAILED =========='
            echo "Build ${env.BUILD_NUMBER} failed. Please check the logs."
        }
    }
}
