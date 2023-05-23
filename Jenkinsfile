#!/usr/bin/env groovy

// this library it's global and defined in jenkins
// @Library('jenkins-shared-library') //we put ' _ ', if don't have other variables after Library

// this library can to use just in this project, it not globally
library identifier: 'jenkins-shared-library@master', retriver: modernSCM(
    [$class: 'GitSCMSource',
    remote: 'https://github.com/Alex99P/jenkins-shared-library.git',
    credentialsID:'github-credentials']
)


def gv

pipeline {
    agent any
    tools{
        maven 'maven-3.6'
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
                   buildImage 'alexpatroi/my-jenkins:jma-3.3'
                   dockerLogin()
                   dockerPush 'alexpatroi/my-jenkins:jma-3.3'
                }
            }
        }
    }
}