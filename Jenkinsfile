pipeline {
    agent any

    tools{
        maven 'maven'
    }

    stages{
        stage('Check and remove container'){
            steps{
                script{
                    def containerExists = sh(script: "docker ps -q -f name=virat", returnStdout: true).trim()
                    if (containerExists) {
                    sh "docker stop virat"
                    sh "docker rm virat"
                    }
                }
            }
        }
        stage('Build package'){
            steps{
                sh 'mvn clean package'
            }
        }
        stage('Create image'){
            steps{
                sh 'sudo docker build -t app /var/lib/jenkins/workspace/kohli/'
            }
        }
        stage('Assign tag'){
            steps{
                sh 'docker tag app sundarvc18/app'
            }
        }
        stage('Push to dockerhub'){
            steps{
                sh 'echo "Sundar@123" | docker login -u "sundarvc18" --password-stdin'
                sh 'docker push sundarvc18/app'
            }
        }
        stage('Remove images'){
            steps{
                sh 'docker rmi -f $(docker images -q)'
            }
        }
        stage('Pull image from DockerHub'){
            steps{
                sh 'docker pull sundarvc18/app'
            }
        }
        stage('Run a container'){
            steps{
                sh 'docker run -it -d --name virat -p 8081:8080 sundarvc18/app'
            }
        }
    }
    post {
        success {
            echo 'Deployment successful'
        }
        failure {
            sh 'docker rm -f virat'
        }
        always{
            echo 'Deployed'
        }
    }

}