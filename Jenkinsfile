pipeline {
    agent any

    environment {
        // CHANGE THIS: Your actual Docker Hub username
        DOCKER_USER_ID = "aravind80555" 
        
        // Image names
        FRONTEND_IMAGE = "${DOCKER_USER_ID}/shop-frontend"
        BACKEND_IMAGE  = "${DOCKER_USER_ID}/shop-backend"
        
        // Unique tag based on Jenkins build numbers (v1, v2, v3...)
        IMAGE_TAG      = "v${BUILD_NUMBER}"
        
        // Infrastructure repository destination
        INFRA_REPO     = "github.com/Aravind162003/Shop-Now_infra.git"
    }

    stages {
        stage('Checkout Application Code') {
            steps {
                cleanWs()
                checkout scm
            }
        }

        stage('Build & Push Frontend') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh '''
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        
                        # Build and push the Vite frontend from its directory
                        docker build -t ${FRONTEND_IMAGE}:${IMAGE_TAG} ./client
                        docker push ${FRONTEND_IMAGE}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Build & Push Backend') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'DOCKER_PASS', usernameVariable: 'DOCKER_USER')]) {
                    sh '''
                        # Build and push the Node.js backend from its directory
                        docker build -t ${BACKEND_IMAGE}:${IMAGE_TAG} ./server
                        docker push ${BACKEND_IMAGE}:${IMAGE_TAG}
                    '''
                }
            }
        }

        stage('Update GitOps Manifest Repo') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'github-token', passwordVariable: 'GIT_PASS', usernameVariable: 'GIT_USER')]) {
                    sh '''
                        # 1. Clone your infrastructure repository
                        git clone https://${GIT_USER}:${GIT_PASS}@${INFRA_REPO}
                        cd Shop-Now_infra/k8s-manifests

                        # 2. Update the frontend deployment manifest image tag
                        sed -i "s|image: ${FRONTEND_IMAGE}:.*|image: ${FRONTEND_IMAGE}:${IMAGE_TAG}|g" frontend-deployment.yaml

                        # 3. Update the backend deployment manifest image tag
                        sed -i "s|image: ${BACKEND_IMAGE}:.*|image: ${BACKEND_IMAGE}:${IMAGE_TAG}|g" backend-deployment.yaml

                        # 4. Configure local git credentials for the automation commit
                        git config user.name "jenkins-bot"
                        git config user.email "jenkins-bot@localhost"

                        # 5. Commit and push changes back to GitHub
                        git add frontend-deployment.yaml backend-deployment.yaml
                        git commit -m "GitOps: Auto-update app images to tag ${IMAGE_TAG}"
                        git push origin master
                    '''
                }
            }
        }
    }
}
