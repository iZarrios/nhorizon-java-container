pipeline {
    agent any

    environment {
        DOCKER_HUB_REPO = 'zarrios/nh-final-project-1'
        IMAGE_TAG = "v1.0"
        DOCKER_CREDENTIALS_ID = 'docker-hub-credentials'  // Jenkins credentials ID for Docker Hub
    }

    stages {

        stage('Build Docker Image') {
            steps {
                script {
                    // Build Docker image with the modified application files
                    sh 'docker build -t $DOCKER_HUB_REPO:$IMAGE_TAG .'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                script {
                    // Log in to Docker Hub and push the image
                    withCredentials([usernamePassword(credentialsId: "$DOCKER_CREDENTIALS_ID", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_USERNAME --password-stdin'
                        sh 'docker push $DOCKER_HUB_REPO:$IMAGE_TAG'
                    }
                }
            }
        }

        stage('Pull Image on Another Agent') {
            steps {
                script {
                    // Assuming a different agent is defined, you can use 'docker pull'
                    sh 'docker pull $DOCKER_HUB_REPO:$IMAGE_TAG'
                }
            }
        }

        stage('Run the Image') {
            steps {
                script {
                    // Run the Docker container on the agent, expose necessary ports
                    sh 'docker run -d -p 8080:80 --name my_app_container $DOCKER_HUB_REPO:$IMAGE_TAG'
                }
            }
        }

        stage('Check Connectivity') {
            steps {
                script {
                    // Check if the website is accessible using curl
                    sh 'sleep 10' // Give some time for the container to be up and running
                    def response = sh(script: 'curl -s -o /dev/null -w "%{http_code}" http://localhost:8080', returnStdout: true).trim()
                    if (response != "200") {
                        error("Website is not accessible. Curl returned HTTP status: ${response}")
                    } else {
                        echo "Website is up and running. Curl returned HTTP status: ${response}"
                    }
                }
            }
        }

    }

    post {
        always {
            script {
                // Clean up the Docker container after the pipeline
                sh 'docker stop my_app_container || true'
                sh 'docker rm my_app_container || true'
            }
        }

        success {
            echo 'Pipeline succeeded, website is accessible!'
        }

        failure {
            echo 'Pipeline failed, website is not accessible.'
        }
    }
}
