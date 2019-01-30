  pipeline {
   agent any
    environment {
      qg = waitForQualityGate()
   
    }
   tools {
        maven 'maven-3' 
    }
    stages{       
 stage('SCM Checkout'){
     steps {
     git 'https://github.com/namjitharavind/jenkins-app.git'
     }
   }
   stage('Compile-Package'){
      steps {
      // Get maven home path
          sh "${mvnHome}/bin/mvn package"
      }
   }
   
   stage('SonarQube Analysis') {
      steps {
          withSonarQubeEnv('sonar-6') { 
          sh "${mvnHome}/bin/mvn sonar:sonar"
        }
      }
    }
    
    stage("Quality Gate Statuc Check"){
       steps {
         script {
          timeout(time: 1, unit: 'HOURS') {
              if (qg.status != 'OK') {
                  error "Pipeline aborted due to quality gate failure: ${qg.status}"
              }
          }
         }
       }
      }
   stage('Deploy to Tomcat'){
      when {
              branch "master"
           }
      steps {
            sshagent(['springboot']) {
              sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@dev-springboot.wisilica.com:/tmp/'
            }
      }
    }
   stage('Email Notification'){
      steps {
      mail bcc: '', body: '''Hi Welcome to jenkins email alerts
      Thanks
      Jenkins''', cc: '', from: 'devjenkins@wisilica.com', replyTo: '', subject: 'Jenkins Job', to: 'naravind@wisilica.com'
      }
   }
  
    }
}

