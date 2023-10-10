pipeline {
	agent { label 'Node1'}
	tools {
	    jdk 'Java17'
	    maven 'maven3'	
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
		
		
	}
}








































