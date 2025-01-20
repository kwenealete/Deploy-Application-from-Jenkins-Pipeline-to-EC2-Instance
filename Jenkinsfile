#!/usr/bin.env groovy

library identifier: 'jenkins-shared-library@master', retriever: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/kwenealete/jenkins-shared-library.git',
    credentialsID: 'github-credentials'
    ]
)

pipeline {   
    agent any
    tool {
        maven 'Maven'
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
                    def dockerCmd = "docker run -d -p 8080:8080 ${IMAGE_NAME}"
                    sshagent(['ec2-server-key']) {
                        sh "ssh -o StrictHostKeyChecking=no ec2-user@54.234.249.131 ${dockerCmd}"
                    }
                }
            }
        }               
    }
} 
