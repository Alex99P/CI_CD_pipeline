#!/usr/bin/env groovy
// this library it's global and defined in jenkins
// @Library('jenkins-shared-library') //we put ' _ ', if don't have other variables after Library

// this library can to use just in this project, it not globally
library identifier: 'jenkins-shared-library@develop', retriver: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/Alex99P/jenkins-shared-library.git',
    credentialsID:'github-credentials']
)


def gv

pipeline {
    agent any
    tools{
        maven 'maven'
    }
    environment {
        IMAGE_NAME ='patroialexandru/my-jenkins:jma-2.0'
    }
    stages {
        stage('init'){
            steps {
                script{
                    gv = load "script.groovy"
                }
            }
        }
        stage('Test') {
            steps {
                script {
                    echo "Testing the application.."
                    echo "Executing pipeline for branch $BRANCH_NAME"
                }
            }
        }
        stage('Build jar') {
            steps {
                 script {
                    buildJar()

                }
            }
        }
        stage('Build image') {
            steps {
                 script {
                //    buildImage 'patroialexandru/my-jenkins:jma-1.0'
                   buildImage(env.IMAGE_NAME)
                   dockerLogin()
                   dockerPush(env.IMAGE_NAME)
                }
            }
        }
        stage('provision server'){
            // we set the env var for terraform
            // we seting the credential to connect to aws account
            environment { 
                AWS_ACCESS_KEY_ID = credentials('jenkins_aws_access_key_id')
                AWS_SECRET_ACCESS_KEY = credentials('jenkins_aws_secret_access_key')
                TF_VAR_env_prefix = 'test'
            }
            steps{
                script{
                    dir('terraform'){ //enter in terraform dir
                        sh "terraform init"
                        sh "terraform apply --auto-approve"
                        EC2_PUBLIC_IP=sh(
                            script: "terraform output ec2_public_ip"
                            returnStdout: true
                            ).trim()
                    }

                }
            }
        }
        stage('Deploy') {
            steps {
                 script {
                    // Give time to server initialization
                    echo "waiting for EC@ server to initialize"
                    sleep(time:110 , unit: "SECONDS")
                    echo "$EC2_PUBLIC_IP"

                    def shellCmd = "bash ./server-cmd.sh ${IMAGE_NAME}"
                    def ec2Instance="ec2-user@${EC2_PUBLIC_IP}"
                    sshagent(['server-ssh-key']) {
                        sh "scp -o StrictHostKeyChecking=no server-cmd.sh ${ec2Instance}:/home/ec2-user"
                        sh "scp -o StrictHostKeyChecking=no docker-compose.yaml ${ec2Instance}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${ec2Instance} ${shellCmd}"
                    }
                }
            }
        }
    }
}