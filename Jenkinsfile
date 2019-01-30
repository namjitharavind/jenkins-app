  pipeline {
   agent any
 
  
    stages{       
 stage('SCM Checkout'){
     steps {
     git 'https://github.com/namjitharavind/jenkins-app.git'
     }
   }
   stage('Compile-Package'){
      steps {
        script{
      // Get maven home path
           def mvnHome =  tool name: 'maven-3', type: 'maven' 
          sh "${mvnHome}/bin/mvn package"
      }
      }
   }
   
   stage('SonarQube Analysis') {
      steps {
        script{
           def mvnHome =  tool name: 'maven-3', type: 'maven' 
           def scannerHome = tool 'SonarQubeScanner'
         withSonarQubeEnv('sonarqube') {
            sh "${scannerHome}/bin/sonar-scanner"
        }
        timeout(time: 1, unit: 'HOURS') {
            waitForQualityGate abortPipeline: true
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

