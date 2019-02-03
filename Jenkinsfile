  pipeline {
   agent any
    stages { 
      
       stage('SCM Checkout') {
         when{
         expression {
           // CHANGE_ID is set only for pull requests, so it is safe to access the pullRequest global variable
           if(env.BRANCH_NAME == 'master' || env.CHANGE_ID){
             return true;
           }
            return false;
          }
        }
         steps {
            git 'https://github.com/namjitharavind/jenkins-app.git'
         }
        }
      
       stage('Compile-Package') {
            when{
         expression {
           // CHANGE_ID is set only for pull requests, so it is safe to access the pullRequest global variable
           if(env.BRANCH_NAME == 'master' || env.CHANGE_ID){
             return true;
           }
            return false;
          }
        }
            steps {
           
              script{
                // Get maven home path
                def mvnHome =  tool name: 'maven-3', type: 'maven' 
               sh "${mvnHome}/bin/mvn package"
              }
            }
        }
   
             
         stage('SonarQube Analysis') {
         when{
         expression {
           // CHANGE_ID is set only for pull requests, so it is safe to access the pullRequest global variable
           if(env.BRANCH_NAME == 'master' || env.CHANGE_ID){
             return true;
           }
            return false;
          }
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
         when{
         expression {
           // CHANGE_ID is set only for pull requests, so it is safe to access the pullRequest global variable
           if(env.BRANCH_NAME == 'master' || env.CHANGE_ID){
             return true;
           }
            return false;
          }
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
             when{
         expression {
           // CHANGE_ID is set only for pull requests, so it is safe to access the pullRequest global variable
           if(env.BRANCH_NAME == 'master'){
             return true;
           }
            return false;
          }
        }
              steps {
                  sshagent(['springboot']) {
                  sh 'scp -o StrictHostKeyChecking=no target/*.war ubuntu@dev-springboot.wisilica.com:/tmp/'
                  }
              }
          }
     
      
    }
    //email
     post {
        success {
           
         when{
         expression {
           // CHANGE_ID is set only for pull requests, so it is safe to access the pullRequest global variable
           if(env.BRANCH_NAME == 'master' || env.CHANGE_ID){
             return true;
           }
            return false;
          }
        }
            steps {
            mail bcc: '', body: '''Hi Welcome to jenkins email alerts
            Thanks
            Jenkins''', cc: '', from: 'devjenkins@wisilica.com', replyTo: '', subject: 'Jenkins Job', to: 'naravind@wisilica.com'
            }
           }
          
      }
  }
 
