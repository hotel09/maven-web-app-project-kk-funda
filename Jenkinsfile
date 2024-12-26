node
{
	properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '5', artifactNumToKeepStr: '5', daysToKeepStr: '5', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])
	
def mHome = tool name: "maven-3.9.9"	

	try(
		stage('checkOut')
	{
	git branch: 'main', credentialsId: '8c2c1a63-e761-4348-89d0-62358b05f0a8', url: 'https://github.com/hotel09/maven-web-app-project-kk-funda.git'
	}

	
	stage('Build')
	{
	sh "${mHome}/bin/mvn clean package"
	}
	
	stage('sonarReport')
	{
	sh "${mHome}/bin/mvn clean sonar:sonar"
	}
	stage('artifactbackup')
	{
	sh "${mHome}/bin/mvn clean deploy"
	}
	
	stage('tomCat')
	{
	sshagent(['c571385b-7415-42cc-93e1-d8e4572a3570']) {
	sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.126.143.169:/opt/apache-tomcat-9.0.97/webapps/"
    
	}
	}
	}
	catch(e){
	// if there is exception throw this will return
	currentBuild.result="FAILED"
	
	}
	finally{
	//success or failure this block will excute
	notifyBuild(currentBuild.result)
	}
	
}


	def notifyBuild(string buildStatus='STARTED'){
	//buildstatus is null means successfull
	buildStatus = buildStatus ?: 'SUCCESS'	

	def colorName='RED'
	def colorCode='#FF0000'
	def subject="${buildStatus}: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]'"
	def summary ="${subject} (${env.BUILD_URL})"
	//Override default values based on build status
	if (buildStatus == 'STARTED'){
	color='YELLOW'
	colorCode='#FFFF00'
	}
	else if (buildStatus == 'SUCCESS'){
	color='GREEN'
	colorCode='#00FF00'
	}
	else{
	color='RED'
	colorCode='#FF0000'
	}

//send slack notification 

	slackSend(color:colorCode, message:summary,channel:vi-dev)


}
