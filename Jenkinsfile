#!/usr/bin.env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/kwenealete/jenkins-shared-library.git',
    credentialsID: 'github-credentials'
    ]
)

pipeline {   
    agent any
    tools {
        maven 'maven-3.9'
    }
    environment {
        IMAGE_NAME = 'monyakwene/demo-app:jma-1.0'
    }
    stages {
        stage("build app") {
            steps {
                script {
                    echo "Building application jar..."
                    buildJar()

                }
            }
        }
        stage("build image") {
            steps {
                script {
                    echo "Building the docker image..."
                    buildImage(env.IMAGE_NAME)
                    dockerLogin()
                    dockerPush(env.IMAGE_NAME)
                }
            }
        }

        stage("deploy") {
            steps {
                script {
                    echo 'Deploying docker image to EC2...'
                    def shellCmd = "bash ./server-cmds.sh"
                    sshagent(['ec2-server-key']) {
                        sh 'scp sever-cmds.sh ec2-user@54.234.249.131:/home/ec2-user'
                        sh 'scp docker-compose.yaml ec2-user@54.234.249.131:/home/ec2-user'
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.234.249.131 ${shellCmd}"
                    }
                }
            }
        }               
    }
} 
