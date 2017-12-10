#!/usr/bin/env groovy

// node {
//     stage('checkout') {
//         checkout scm
//     }

//     docker.image('maven:3-alpine'){

//         stage('clean') {
//             sh "chmod +x mvnw"
//             sh "./mvnw clean"
//         }

//         stage('backend tests') {
//             try {
//                 sh "./mvnw test"
//             } catch(err) {
//                 throw err
//             } finally {
//                 junit '**/target/surefire-reports/TEST-*.xml'
//             }
//         }

//         stage('packaging') {
//             sh "./mvnw verify -Pprod -DskipTests"
//             archiveArtifacts artifacts: '**/target/*.war', fingerprint: true
//         }

//         stage('quality analysis') {
//             withSonarQubeEnv('Sonar') {
//                 sh "./mvnw sonar:sonar"
//             }
//         }
//     }

//     def dockerImage
//     stage('build docker') {
//         sh "cp -R src/main/docker target/"
//         sh "cp target/*.war target/docker/"
//         dockerImage = docker.build('astefanich/handlingeventservice', 'target/docker')
//     }

//     stage('publish docker') {
//         docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
//             dockerImage.push 'latest'
//         }
//     }
// }


// #!/usr/bin/env groovy
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
                // post {
                //     always {
                //         // sh 'mkdir -p build/reports'
                //         // sh 'rm -rf build/reports/*'
                //         // sh 'docker cp JENKINS:build/reports **/target/surefire-reports/TEST-*.xml'
                //         // junit '**/target/surefire-reports/TEST-*.xml'
                //         // sh 'make check || true'
                //         // junit '**/target/*.xml'
                //         junit 'target/surefire-reports/*.xml'
                //     }
                // }
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

        // node {
        //     stage('docker build/push'){
        //         docker.withRegistry('https://registry.hub.docker.com', 'dockerhub'){
        //                 sh "cp -R src/main/docker target/"
        //                 sh "cp target/*.war target/docker/"
        //                 def app = docker.build("astefanich/bookingservice:${commit_id}", 'target/docker').push()
        //         }
        //     }
        // }

    } //end of stages
} //end of pipeline
  