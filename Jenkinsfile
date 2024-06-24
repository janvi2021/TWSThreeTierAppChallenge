pipeline {
    agent any

    environment {
        // GITHUB_CREDENTIALS = 'github-credentials-id' // The ID you set for the GitHub PAT
        
        // GIT_REPO_URL = 'https://github.com/janvi2021/TWSThreeTierAppChallenge.git' // Your GitHub repo URL
        
        
        
        DOCKER_HUB_CREDENTIALS = 'dockerhub-credentials-id' // Your Docker Hub credentials ID
        DOCKER_HUB_REPO = 'janvi20/tws_application' // Your Docker Hub repo
        DOCKER_IMAGE_TAG = "${DOCKER_HUB_REPO}:${BUILD_NUMBER}" // Image tag using build number
    }

    stages {
        // stage('Checkout Code') {
        //     steps {
        //         git credentialsId: "${GITHUB_CREDENTIALS}", url: "${GIT_REPO_URL}"
        //     }
        // }

        stage('Build Docker Image') {
            steps {
                script {
                    dir('Jenkins-Pipeline-Code/Jenkinsfile-Frontend') { // Adjust this to your directory structure
                        sh "docker build -t ${DOCKER_IMAGE_TAG} ."
                    }
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: "${DOCKER_HUB_CREDENTIALS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh """
                            echo ${DOCKER_PASSWORD} | docker login -u ${DOCKER_USER} --password-stdin
                            docker push ${DOCKER_IMAGE_TAG}
                            docker logout
                        """
                    }
                }
            }
        }


        stage('Run Docker Container') {
            steps {
                script {
                    sh "docker run -d --name frontend -p 80:80 ${DOCKER_IMAGE_TAG}"
                }
            }
        }
    }

    post {
        always {
            script {
                sh "docker container prune -f"
                sh "docker image prune -f"
            }
        }
    }
}
