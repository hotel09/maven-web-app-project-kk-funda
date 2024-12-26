node
{
	properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '5', artifactNumToKeepStr: '5', daysToKeepStr: '5', numToKeepStr: '5')), pipelineTriggers([pollSCM('* * * * *')])])

	
	stage('checkOut')
	{
	git branch: 'main', credentialsId: '8c2c1a63-e761-4348-89d0-62358b05f0a8', url: 'https://github.com/hotel09/maven-web-app-project-kk-funda.git'
	}

	def mHome = tool name: "maven-3.9.9"
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
