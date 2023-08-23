pipeline {
	agent any
	stages {
    	  stage('Build Artifact') {
         	   steps {
            	  sh "mvn clean package -DskipTests=true"
            	  archive 'target/*.jar'  //test
            }
        }   
//--------------------------------------------------------------------------------------	    
     	 stage('UNIT test & jacoco ') {
      	   steps {
      		  sh "mvn test"
     	 }
    	  post {
      		  always {
       			   junit 'target/surefire-reports/*.xml'
          		   jacoco execPattern: 'target/jacoco.exec'
        }
      }

    }	

//--------------------------------------------------------------------------------------
	 stage('Mutation Tests - PIT') {
      steps {
        sh "mvn org.pitest:pitest-maven:mutationCoverage"
      }
        post { 
         always { 
           pitmutation mutationStatsFile: '**/target/pit-reports/**/mutations.xml'
         }
       }
    }

//---------------------------------------------------------------------------------------
		stage('Docker Build and Push') {
      steps {
        withCredentials([string(credentialsId: 'github-samitoto', variable: 'DOCKER_HUB_PASSWORD')]) {
          sh 'sudo docker login -u samitoto -p $DOCKER_HUB_PASSWORD'
          sh 'printenv'
          sh 'sudo docker build -t samitoto/devops-javaapp:""$GIT_COMMIT"" .'
          sh 'sudo docker push samitoto/devops-javaapp:""$GIT_COMMIT""'
        }
 
      }
    }
	}
}
