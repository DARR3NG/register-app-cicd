pipeline {
  agent { label 'Jenkins-Agent'}
  tools {
	jdk 'Java17'
	maven 'Maven3'
  }

 stages {
	 stage ('Cleanup Workspace'){
		 steps{
			 cleanWs()
		 }
	 }

	 stage('Checkout from SCM'){
		 steps{
			 git branch:'main', credentialsId: 'github', url: 'https://github.com/DARR3NG/register-app-cicd'
		 }
	 }

	 stage('Build App'){
		 steps{
			 sh "mvn clean package"
		 }
	 }

	 stage('Test App'){
		 steps{
			 sh "mvn test"
		 }
	 }
 }
}
