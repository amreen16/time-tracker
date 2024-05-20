pipeline {
    agent any

    tools {
        maven 'Maven'
        // Add this line to use the Docker installation configured in Jenkins
        dockerTool 'Docker'
    }

    environment {
        MAVEN_OPTS = '-Xmx1024m'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/amreen16/time-tracker.git', branch: 'master'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def app = docker.build("my-app:${env.BUILD_ID}")
                }
            }
        }

        stage('Deploy Docker Container') {
            steps {
                script {
                    // Stop and remove any existing container
                    try {
                        bat 'docker stop my-app'
                        bat 'docker rm my-app'
                    } catch (Exception e) {
                        echo 'No existing container to remove'
                    }

                    // Run the new container
                    bat 'docker run -d --name my-app -p 8080:8080 my-app:${env.BUILD_ID}'
                }
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/target/*.jar', allowEmptyArchive: true
        }

        success {
            echo 'Build succeeded!'
        }

        failure {
            echo 'Build failed!'
        }
    }
}
