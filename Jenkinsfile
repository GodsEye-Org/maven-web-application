node {
    
    def mavenHome = tool name: "maven3.8.6"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '5', artifactNumToKeepStr: '5', daysToKeepStr: '5', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
    
    try {
    stage ('checkout'){
    git branch: 'development', credentialsId: 'ff7d6067-3be9-4b9b-b1c9-a54a11c33b56', url: 'https://github.com/GodsEye-Org/maven-web-application.git'
    }
    stage ('Build') {
    sh "${mavenHome}/bin/mvn clean package"     
    }
    stage ('Executesonarqubereport'){
    sh "${mavenHome}/bin/mvn sonar:sonar"    
    }
    stage ('UploadtoNexus'){
    sh "${mavenHome}/bin/mvn deploy"    
    }
    stage ('DepoyinTomcat'){
    sshagent(['211021b8-30d4-4551-b080-009cd89e780d']){ 
    sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@52.66.253.54:/opt/apache-tomcat-9.0.64/webapps/"
    }
    }
    }
    catch (e) {
    currentBuild.result = "FAILURE"
    throw e
    }
    finally {
    slacknotification(currentBuild.result)
    }
}
    //slack notification block
    def slacknotification(String buildStatus = 'STARTED') {
 
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  // Default values
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  // Override default values based on build status
  if (buildStatus == 'STARTED') {
    color = 'YELLOW'
    colorCode = '#FFFF00'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  // Send notifications
  slackSend (color: colorCode, message: summary)
}

