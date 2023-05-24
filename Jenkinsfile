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
        stage('Connect to ec2'){
            steps {
                script {
                    def dockerCMD = 'docker run -p 3080:3080 -d patroialexandru/my-jenkins:jma-1.0'
                    sshagent(['ec2-user-id-rsa']) {
                    sh "ssh -o StrictHostKeyChecking=no ubuntu@ec2-3-74-158-201.eu-central-1.compute.amazonaws.com ${dockerCMD}"
                }
                    
                }
            }
        }
    }
}