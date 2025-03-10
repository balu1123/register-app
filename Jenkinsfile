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
		DOCKER_PASS = 'dockerhub-cred'
		IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
		IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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
			 docker.withRegistry('',DOCKER_PASS) {
			      docker_image.push("${IMAGE_TAG}")
			      docker_image.push('latest')	 
			 }   
		   }		
		}   
	     }	

	  stage("Trivy Scan"){
		steps {
	            script {
			 sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image bbaludevops/register-app-pipeline:latest --no-progress --scanners vuln --exit-code 0 --severity HIGH,CRITICAL --format table')   
		    }
		}
	     }	  

	stage("Cleanup Artifacts"){
		steps {
	            script {
			 sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
			 sh "docker rmi ${IMAGE_NAME}:latest"  
		    }
		}
	     }	  	
		
    }
}








































