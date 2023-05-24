#!/usr/bin/env groovy
// this library it's global and defined in jenkins
@Library('jenkins-shared-library') //we put ' _ ', if don't have other variables after Library

// this library can to use just in this project, it not globally
// library identifier: 'jenkins-shared-library@master', retriver: modernSCM(
//     [$class: 'GitSCMSource',
//     remote: 'https://github.com/Alex99P/jenkins-shared-library.git',
//     credentialsID:'1c50f359-09e8-49ee-9088-80c8f722fabf']
// )


def gv

pipeline {
    agent any
    tools{
        maven 'maven'
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
                //    buildImage 'alexpatroi/my-jenkins:jma-1.0'
                   buildImage()               
                   dockerLogin()
                   dockerPush 'alexpatroi/my-jenkins:jma-1.0'
                }
            }
        }
    }
}