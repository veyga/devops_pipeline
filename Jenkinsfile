#!/usr/bin/env groovy
pipeline {
    agent { 
            docker  {
                image 'maven:3-alpine'
                args '-v /root/.m2:/root/.m2'
            }
        }

    stages {
        
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Clean'){
            steps {
                sh "mvn test"
            }
        }

        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
               
        }

        stage('Package'){
            steps{
                    sh "mvn verify -Pprod -DskipTests"
                    archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
            } 
        }

        stage('Code Analysis') {
            steps {
                withSonarQubeEnv('Sonar') {
                    sh "mvn sonar:sonar -Dsonar.host.url=http://192.241.210.80:9000"
                }
            }
        }

    } //end of stages
} //end of pipeline
  