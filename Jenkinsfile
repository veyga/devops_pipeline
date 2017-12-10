#!/usr/bin/env groovy

node {
    stage('checkout') {
        checkout scm
    }

    docker.image('maven:3-alpine'){

        stage('clean') {
            sh "chmod +x mvnw"
            sh "./mvnw clean"
        }

        stage('backend tests') {
            try {
                sh "./mvnw test"
            } catch(err) {
                throw err
            } finally {
                junit '**/target/surefire-reports/TEST-*.xml'
            }
        }

        stage('packaging') {
            sh "./mvnw verify -Pprod -DskipTests"
            archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
        }

        stage('quality analysis') {
            withSonarQubeEnv('Sonar') {
                sh "./mvnw sonar:sonar"
            }
        }
    }

    def dockerImage
    stage('build docker') {
        sh "cp -R src/main/docker target/"
        sh "cp target/*.war target/docker/"
        dockerImage = docker.build('astefanich/handlingeventservice', 'target/docker')
    }

    stage('publish docker') {
        docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
            dockerImage.push 'latest'
        }
    }
}


// #!/usr/bin/env groovy
// pipeline {
//     agent { docker 'maven:3-alpine' }

//     stages {
        
//         stage('Checkout') {
//             steps {
//                 checkout scm
//                 sh "git rev-parse --short HEAD > .git/commit-id"
//                 commit_id = readFile('.git/commit-id').trim()
//             }
//         }

//         stage('Clean'){
//             steps {
//                 sh "mvn test"
//             }
//         }

//         stage('Test') {
//             steps {
//                 sh 'mvn test'
//             }
//                 post {
//                     always {
//                         junit '**/target/surefire-reports/TEST-*.xml'
//                     }
//                 }
//         }

//         stage('Package'){
//             steps{
//                     sh "mvn verify -Pprod -DskipTests"
//                     archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
//             } 
//         }

//         stage('Code Analysis') {
//             steps {
//                 withSonarQubeEnv('Sonar') {
//                     sh "mvn sonar:sonar -Dsonar.host.url=http://192.241.210.80:9000"
//                 }
//             }
//         }

//         stage('Docker Build/Push'){
//             steps{
//                 docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
//                     sh "cp -R src/main/docker target/"
//                     sh "cp target/*.war target/docker/"
//                     def app = docker.build("astefanich/bookingservice:${commit_id}", 'target/docker').push()
//                 }
//             }
//         }
    

//     } //end of stages
// } //end of pipeline

// // node {
// //     def commit_id
// //     stage('checkout') {
// //         checkout scm
// //         sh "git rev-parse --short HEAD > .git/commit-id"
// //         commit_id = readFile('.git/commit-id').trim()
// //     }

// //     docker.image('maven:3.5.2-jdk-8-alpine'){
// //         stage('check java') {
// //             sh "java -version"
            
// //         }

// //         stage('clean') {
// //             // sh "chmod +x mvnw"
// //             sh "mvn clean"
// //         }

// //         stage('backend tests') {
// //             try {
// //                 sh "mvn test"
// //             } catch(err) {
// //                 throw err
// //             } finally {
// //                 junit '**/target/surefire-reports/TEST-*.xml'
// //             }
// //         }

// //         stage('packaging') {
// //             sh "mvn verify -Pprod -DskipTests"
// //             archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
// //         }

// //         stage('code analysis') {
// //             withSonarQubeEnv('Sonar') {
// //                 sh "mvn sonar:sonar -Dsonar.host.url=http://192.241.210.80:9000"
// //             }
// //         }
// //     }

// //     stage('docker build/push'){
// //         docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
// //             sh "cp -R src/main/docker target/"
// //             sh "cp target/*.war target/docker/"
// //             def app = docker.build("astefanich/bookingservice:${commit_id}", 'target/docker').push()
// //         }
// //     }
// // }