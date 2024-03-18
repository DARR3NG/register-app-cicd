pipeline {
  agent { label 'Jenkins-Agent'}
  tools {
	jdk 'Java17'
	maven 'Maven3'
  }


  environment{
	  APP_NAME = "register-app-pipeline"
	  RELEASE = "1.0.0"
	  DOCKER_USER = "darr3ng"
	  DOCKER_PASS = "dockerhub"
	  IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
	  IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	  
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

	 stage('Sonrqube Analysis'){
		 steps{
			 script{
			 	withSonarQubeEnv(credentialsId:'jenkins-sonarqube-token'){
				 	sh "mvn sonar:sonar"
			 	}
			}
		 }
	 }

	 stage('Quality Gate'){
		 steps{
		 	script{
			 	waitForQualityGate abortPipline: false, credentialsId: 'jenkins-sonarqube-token'
		 	}
		 }
	 }

	 stage('Build & Push Docker Image'){
		 steps{
			 script{
				 docker.withRegistry('',DOCKER_PASS){
					 docker_image= docker.build "${IMAGE_NAME}"
				 }
				 docker.withRegistry('',DOCKER_PASS){
					 docker_image.push("${IMAGE_TAG}")
					 docker_image.push('latest')
				 }
			 }
		 }
		 
	 }

	 stage('Trivy Scan'){
		 steps{
			 script{
				 sh ('docker run -v /var/run/docker.stock aqusec/trivy image darr3ng/register-app-pipeline:latest --no-progress --scanners vuln --exit-code 0 --sverity HIGH,CRITICAL --format table')
			 }
		 }
	 }

	 stage('Cleanup Artifacts'){
		 steps{
			 script{
				 sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
				 sh "docker rmi ${IMAGE_NAME}:latest"
			 }
		 }
	 }

	 
 }
}
