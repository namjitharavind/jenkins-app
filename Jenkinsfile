  pipeline {
   agent any
    stages { 
      
       stage('SCM Checkout') {
         when {
                branch "master"
              }
         steps {
            git 'https://github.com/namjitharavind/jenkins-app.git'
         }
        }
      
       stage('Compile-Package') {
            steps {
              when {
                branch "master"
              }
              script{
                // Get maven home path
                def mvnHome =  tool name: 'maven-3', type: 'maven' 
               sh "${mvnHome}/bin/mvn package"
              }
            }
        }
   
             
         stage('SonarQube Analysis') {
           when {
                branch "master"
              }
              steps {
                script {
                    def mvnHome =  tool name: 'maven-3', type: 'maven' 
                     withSonarQubeEnv('sonar-6') { 
                      sh "${mvnHome}/bin/mvn sonar:sonar"
                      }
                }
              }
         }
    
      
         stage("Quality Gate Statuc Check") {
           when {
                branch "master"
              }
              steps {
                script {
                  def qg = waitForQualityGate()
                  timeout(time: 1, unit: 'HOURS') {
                  if (qg.status != 'OK') {
                    error "Pipeline aborted due to quality gate failure: ${qg.status}"
                  }
                }
              }
          }
         }
           
          stage('Deploy to Tomcat') {
              when {
                branch "master"
              }
              steps {
                  sshagent(['springboot']) {
                  sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@dev-springboot.wisilica.com:/tmp/'
                  }
              }
          }
           stage('Email Notification') {
             when {
                branch "master"
              }
            steps {
            mail bcc: '', body: '''Hi Welcome to jenkins email alerts
            Thanks
            Jenkins''', cc: '', from: 'devjenkins@wisilica.com', replyTo: '', subject: 'Jenkins Job', to: 'naravind@wisilica.com'
            }
          }
      
      
    }
  }
 
