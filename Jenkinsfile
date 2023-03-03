pipeline {
    agent any
    environment{
        IMAGE_NAME= 'pm310/spring_exam-app:6.1'
    }

    stages {
        stage("init") {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage("build jar") {
            steps {
                script {
                    echo "building the spring application"
                    sh 'mvn package'
                }
            }
        }
        stage("build image") {
            steps {
                script {
                    echo "building the docker image and push to docker hub..."
                    //gv.buildImage()
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-pm310', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]){
                        sh 'docker build -t ${IMAGE_NAME} .'
                        sh 'docker login -u $USERNAME -p $PASSWORD'
                        sh 'docker push ${IMAGE_NAME}'
                    }
                }
            }
        }
        stage("deploy") {
            steps {
                script {
                    echo "deploying the spring application on ec2"
                    //gv.deployApp()
                    def dockerStop="docker stop ec2-spring"
                    def dockerDelete="docker rm ec2-spring"
                    def dockerCreate="docker run -p 443:443 --name ec2-spring ${IMAGE_NAME}"

                    sshagent(['ec2-ubuntu-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.95.222.132 ${dockerStop}"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.95.222.132 ${dockerDelete}"
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.95.222.132 ${dockerCreate}"

                    }
                }
            }
        }
    }
}
