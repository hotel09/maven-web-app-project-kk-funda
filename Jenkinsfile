pipeline {
	agent any
	
	tools{
	maven "maven-3.9.9"
	}
	
	stages{
	    
	stage('checkOut'){
	steps{
	git branch: 'dev', credentialsId: '8c2c1a63-e761-4348-89d0-62358b05f0a8', url: 'https://github.com/hotel09/maven-web-app-project-kk-funda.git'

	}
	}
	
	stage('build'){
	steps{
	sh "mvn clean package"
	}
	}

	stage('sonarReport'){
	steps{
	sh "mvn clean sonar:sonar"

	}
	}

	stage('artifacBackup'){
	steps{
	sh "mvn clean deploy"

	}
	}

	stage('deplyTomcat'){
	steps{
	sshagent(['c571385b-7415-42cc-93e1-d8e4572a3570']) {
	sh "scp -o StrictHostKeyChecking=no target/maven-web-application.war ec2-user@13.126.143.169:/opt/apache-tomcat-9.0.97/webapps/"
	}
	}

	

}

}//stages closing

post{
	success{
	notifyBuild(currentBuild.result)
	}

	failure{
	notifyBuild(currentBuild.result)
	}
}	

} //pipeline closing



def notifyBuild(String buildStatus='STARTED'){
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

	slackSend(color:colorCode, message:summary)


}
