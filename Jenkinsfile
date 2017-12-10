#!/usr/bin/env groovy

node {
    def commit_id
    stage('checkout') {
        checkout scm
        sh "git rev-parse --short HEAD > .git/commit-id"
        commit_id = readFile('.git/commit-id').trim()
    }

    docker.image('openjdk:8').inside('-u root -e MAVEN_OPTS="-Duser.home=./"') {
        stage('check java') {
            sh "java -version"
        }

        stage('clean') {
            // sh "chmod +x mvnw"
            sh "mvn clean"
        }

        stage('backend tests') {
            try {
                sh "mvn test"
            } catch(err) {
                throw err
            } finally {
                junit '**/target/surefire-reports/TEST-*.xml'
            }
        }

        stage('packaging') {
            sh "mvn verify -Pprod -DskipTests"
            archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
        }

        stage('quality analysis') {
            withSonarQubeEnv('Sonar') {
                sh "mvn sonar:sonar -Dsonar.host.url=http://192.241.210.80:9000"
            }
        }
    }

    stage('docker build/push'){
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
            sh "cp -R src/main/docker target/"
            sh "cp target/*.war target/docker/"
            def app = docker.build("astefanich/bookingservice:${commit_id}", 'target/docker').push()
        }
    }
}