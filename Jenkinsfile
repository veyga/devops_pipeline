node {
   def mvnHome
   stage('Prepare') {
      git url: 'git@github.com:astefanich/devops_pipeline.git', branch: 'develop'
      mvnHome = tool 'maven'
   }
   stage('Build') {
         sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean package"
   }
   stage('Unit Test') {
      junit '**/target/surefire-reports/TEST-*.xml'
      archive 'target/*.jar'
   }
   stage('Integration Test') {
        sh "'${mvnHome}/bin/mvn' -Dmaven.test.failure.ignore clean verify"
   }
   stage('Sonar') {
         sh "'${mvnHome}/bin/mvn' sonar:sonar"
   }
   stage('Deploy') {
       sh 'curl -u jenkins:jenkins -T target/**.war "http://localhost:8888/manager/text/deploy?path=/devops&update=true"'
   }
   stage("Smoke Test"){
       sh "curl --retry-delay 10 --retry 5 http://localhost:8888/devops"
   }
}