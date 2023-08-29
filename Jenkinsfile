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
	//	
 	stage ('Deploy to server') {
            steps {
 		    timeout(time: 4, unit: 'MINUTES') {
	        	   sshagent(['app-server']) {
                sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/infra-job/target/webgoat-2023.5-SNAPSHOT.jar ubuntu@13.235.134.242:/webgoat_devsecops'
		//sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.235.134.242 "nohup java -jar /home/ubuntu/webgoat_devsecops/webgoat-2023.5-SNAPSHOT.jar --server.address=13.235.134.242 --server.port=8080 &"'
 				
           		}
		    }
            	}     
    	}
	
 	 stage ('Dynamic analysis') {
            steps {
          	  sshagent(['dast-scan']) {
            sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.126.249.45 "sudo docker run --rm -v /home/ubuntu:/zap/wrk/:rw -t owasp/zap2docker-stable zap-full-scan.py -t http://13.235.134.242:8080/WebGoat -x zap_report || true" '
			
                  }      
            }       
    }
    }
}
