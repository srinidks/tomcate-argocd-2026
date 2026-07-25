pipeline {
    agent any
    tools {
        maven 'maven-3.5.0'
        }
    environment {
        IMAGE_NAME = "srinidks/argocd-tomcat-app"
        IMAGE_TAG  = "${BUILD_NUMBER}"
    }

    stages {

        stage('Checkout code from GitHub') {
            steps {
                git branch: 'main', credentialsId: 'GitHub', url: 'https://github.com/srinidks/tomcate-argocd-2026.git'
            }
        }
        stage('Build the Application') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Code Quality check') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn sonar:sonar'
                    }
            }
        }
        stage('Create Docker Image') {
            steps {
                sh 'docker build -t $IMAGE_NAME:$IMAGE_TAG .'
            }
        }
        stage('Push docker image to docker HUB') {
            steps {
                withDockerRegistry(credentialsId: 'DockerHub', url: 'https://index.docker.io/v1/') {
                    sh 'docker push $IMAGE_NAME:$IMAGE_TAG'
                }
            }
        }
        stage('Update Kubernetes Manifest for ArgoCD') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'GitHub1', usernameVariable: 'GIT_USER', passwordVariable: 'GIT_TOKEN')]) {
                    sh '''
                        sed -i "s|image: ${IMAGE_NAME}:.*|image: ${IMAGE_NAME}:${IMAGE_TAG}|g" manifest/deployment.yaml

                        git config user.email "jenkins@cicd.local"
                        git config user.name "Jenkins CI"

                        git add manifest/deployment.yaml
                        git commit -m "Automated CI: Update image tag to ${IMAGE_TAG} [skip ci]" || echo "No changes to commit"

                        git push https://${GIT_USER}:${GIT_TOKEN}@github.com/srinidks/tomcate-argocd-2026.git HEAD:main
                    '''
                }
            }
        }

    } // stages end
} // pipeline end

