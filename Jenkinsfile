node
{
    def mavenHome = tool name: "maven3.8.5"
    properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '5', daysToKeepStr: '', numToKeepStr: '5')), [$class: 'JobLocalConfiguration', changeReasonComment: ''], pipelineTriggers([pollSCM('* * * * *')])])
    echo "The job name is: ${env.JOB_NAME}"
    echo "The node name is: ${env.NODE_NAME}"
    echo "The workspace path is: ${env.WORKSPACE}"
    echo "The node label is: ${env.NODE_LABELS}"
    echo "The ebuild number is: ${env.BUILD_NUMBER}"
    try{
        slacknotifications("STARTED")
        
    stage('CheckoutCode'){
        git branch: 'development', 
        credentialsId: '8591c5c6-4b57-4739-bcfc-363510c00960', 
        url: 'https://github.com/kushpvgdevops/maven-web-application.git'
    }
    stage('Build'){
        sh "${mavenHome}/bin/mvn clean package"
    }
  /*  stage('ExecuteSonarQubeReport'){
        sh "${mavenHome}/bin/mvn clean package sonar:sonar"
    }
    stage('UploadArtifactsIntoNexus'){
        sh "${mavenHome}/bin/mvn deploy"
    }
    stage('DeployAppIntoTomcatServer'){
        sshagent(['c82d8db2-1c5b-4787-a113-9d7f62175bf1']) {
        sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.232.129.149:/opt/apache-tomcat-9.0.64/webapps/"
       }
    }
*/
}
    catch(e){
    currentBuild.result = "FAILURE"
    throw e
    }
    finally{
    slacknotifications(currentBuild.result)
    }
}

def slacknotifications(String buildStatus = 'STARTED') {
  
  buildStatus =  buildStatus ?: 'SUCCESSFUL'

  
  def colorName = 'RED'
  def colorCode = '#FF0000'
  def subject = "${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
  def summary = "${subject} (${env.BUILD_URL})"

  
  if (buildStatus == 'STARTED') {
    color = 'ORANGE'
    colorCode = '#FFA500'
  } else if (buildStatus == 'SUCCESSFUL') {
    color = 'GREEN'
    colorCode = '#00FF00'
  } else {
    color = 'RED'
    colorCode = '#FF0000'
  }

  
  slackSend (color: colorCode, message: summary)
}



