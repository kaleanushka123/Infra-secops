pipeline {
	agent any

	stages {
		stage ('Initialize') {
		      steps {
			sh '''      
		        	echo "PATH = ${PATH}"
	   			echo "M2_HOME = ${M2_HOME}"
       			'''
      			}
    		}
            stage ('Check Secrets') {
     			steps {
 			    sh 'trufflehog3 https://github.com/kaleanushka123/Infra-secops.git -f json -o truffelhog_output.json || true'
 			      
			}
    		}
            stage('Compile and Build'){
 		steps{
 			sh 'mvn clean install -DskipTests'
			}
 		}
 	stage ('Deploy to server') {
            steps {
 		    timeout(time: 4, unit: 'MINUTES') {
	        	   sshagent(['application-server']) {
                sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/webgoat-devsecops/webgoat-server/target/webgoat-server-v8.2.0-SNAPSHOT.jar ubuntu@13.235.245.236:/WebGoat'
 				
           		}
		    }
            	}     
    	}
	
 	 stage ('Dynamic analysis') {
            steps {
          	  sshagent(['application_server']) {
            sh 'ssh -o  StrictHostKeyChecking=no ubuntu@18.212.157.207 "sudo docker run --rm -v /home/ubuntu:/zap/wrk/:rw -t owasp/zap2docker-stable zap-full-scan.py -t http://13.235.245.236:8080/WebGoat -x zap_report || true" '
			
                  }      
            }       
    }
    }
}