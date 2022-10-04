try{
	node{
	    properties([parameters([choice(choices: ['master', 'dev', 'qa', 'staging'], description: 'Choose branch to build and deploy', name: 'gitBranch')]), pipelineTriggers([pollSCM('')])])
    stage('Git Checkout'){
		git credentialsId: 'github', 
		    url: 'https://github.com/SuryaAkana117/myweb.git',
			branch: "${params.gitBranch}"
	}
	
	stage('Maven Build'){
		sh 'mvn clean package'
	}
	stage('Deploy to Dev'){
		sh 'mv target/*.war target/myweb.war'
		sshagent(['tomcat-dev']) {
			sh 'ssh ec2-user@172.31.34.246 rm -rf /opt/tomcat8/webapps/myweb*'
		    sh 'scp target/myweb.war ec2-user@172.31.34.246 /opt/tomcat8/webapps/'
		    sh 'ssh ec2-user@172.31.34.246 sudo service tomcat restart'
		}
	    slackSend channel: '#devops-2',
				  color: 'good',
				  message: "Job -  ${env.JOB_NAME}, Completed successfully Build URL is ${env.BUILD_URL}"


	}
}

}catch(error){
  slackSend channel: '#devops-2',
				  color: 'danger',
				  message: "Job -  ${env.JOB_NAME}, Failed, Build URL is ${env.BUILD_URL}"
   error 'Something wrong'
}
