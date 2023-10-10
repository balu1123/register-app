pipeline {
	agent { label 'Node1'}
	tools {
	    jdk 'Java17'
	    maven 'maven3'	
	}

	environment {
		APP_NAME = "register-app-pipeline"
		RELEASE = "1.0.0"
		DOCKER_USER = "bbaludevops"
		DOCKER_PASS = 'Baluvijaya@2020'
		IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
		IMAGE_TAG = "${RELEASE} - ${BUILD_NUMBER}"
	}
	
	stages{
	     stage("Cleanup Workspace"){
		steps {
		     cleanWs()     
		     }
	     }

	     stage("CheckOut from SCM"){
		 steps{
		     git branch: 'main', url: 'https://github.com/balu1123/register-app.git'	     
		     }
	     } 	

	    stage("Build Application"){
		steps {
		    sh "mvn clean package"	    
		    }
	    }

	    stage("Test Application"){
		steps {
		    sh "mvn test"	
		}    
	    }	
		
	   stage("Sonarqube Analysis"){
		steps {
	            script {
	        	withSonarQubeEnv(credentialsId: 'sonar') {
                        sh "mvn sonar:sonar"
                      }	
		   }		
		}   
	     }
		
           stage("Quality Gate"){
		steps {
	            script {
	        	waitForQualityGate abortPipeline: false, credentialsId: 'sonar' 	
		   }		
		}   
	     }

	stage("Build & push Docker image"){
		steps {
	            script {
	        	docker.withRegistry('',DOCKER_PASS) {
		             docker_image = docker.build "${IMAGE_NAME}"		
			}
			 docker.withregistry('',DOCKER_PASS) {
			      docker_image.push("${IMAGE_TAG}")
			      docker_image.push('latest')	 
			 }   
		   }		
		}   
	     }	
		
	}
}








































