pipeline {
    agent {
        docker {
            image 'venkat1451/eclipse-venkat-docker-agent:v3'
            args '--user root -v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        DOCKER_IMAGE = "venkat1451/ultimate-cicd:${BUILD_NUMBER}"
        SONAR_URL = "http://54.234.14.79:9000"
        GIT_REPO_NAME = "Jenkins-Zero-To-Hero"
        GIT_USER_NAME = "Venkateswaran1451"
    }

    stages {

        stage('Checkout') {
            steps {
                echo "Checking out source code..."
                git branch: 'main', url: 'https://github.com/Venkateswaran1451/Jenkins-Zero-To-Hero.git'
            }
        }

        stage('Build and Test') {
            steps {
                echo "Building Maven project..."
                sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn clean package -DskipTests'
            }
        }

        stage('Static Code Analysis') {
            steps {
                echo "Running SonarQube analysis..."
                withCredentials([string(credentialsId: 'sonarqube', variable: 'SONAR_AUTH_TOKEN')]) {
                    sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && mvn sonar:sonar -Dsonar.login=$SONAR_AUTH_TOKEN -Dsonar.host.url=${SONAR_URL}'
                }
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                echo "Building Docker image..."
                script {
                    sh 'cd java-maven-sonar-argocd-helm-k8s/spring-boot-app && docker build -t ${DOCKER_IMAGE} .'
                    def dockerImage = docker.image("${DOCKER_IMAGE}")
                    docker.withRegistry('https://index.docker.io/v1/', 'docker-cred') {
                        dockerImage.push()
                    }
                }
            }
        }

        stage('Update Deployment File') {
            steps {
                echo "Running Update Deployment File stage..."
                withCredentials([string(credentialsId: 'github', variable: 'GITHUB_TOKEN')]) {
                    timeout(time: 5, unit: 'MINUTES') {
                        sh '''
                            echo "Current directory: $(pwd)"
                            git --version
                            
                            # Trust the workspace
                            git config --global --add safe.directory $(pwd)

                            # Configure Git user
                            git config --global user.email "Venkateswaran1451@users.noreply.github.com"
                            git config --global user.name "Venkateswaran1451"

                            # Ensure branch is main
                            git checkout main || git checkout -b main
                            git fetch origin
                            git reset --hard origin/main

                            # Update deployment.yml image tag
                            BUILD_NUMBER=${BUILD_NUMBER}
                            DEPLOY_FILE="java-maven-sonar-argocd-helm-k8s/spring-boot-app-manifests/deployment.yml"
                            echo "Updating $DEPLOY_FILE with BUILD_NUMBER=${BUILD_NUMBER}"
                            sed -i "s/replaceImageTag/${BUILD_NUMBER}/g" $DEPLOY_FILE

                            # Commit & push only if changes exist
                            git add $DEPLOY_FILE
                            if ! git diff --cached --quiet; then
                                git commit -m "Update deployment image to version ${BUILD_NUMBER}"
                                git push https://${GITHUB_TOKEN}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME}.git HEAD:main --quiet
                                echo "Deployment file updated and pushed."
                            else
                                echo "No changes to commit."
                            fi
                        '''
                    }
                }
            }
        }

    } // end stages
} // end pipeline
