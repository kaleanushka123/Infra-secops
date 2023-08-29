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
	        	   sshagent(['web-server']) {
                sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/infra-job/target/webgoat-2023.5-SNAPSHOT.jar app3@13.126.140.19:WebGoat'
		sh 'ssh -o  StrictHostKeyChecking=no app3@13.126.140.19 "nohup java -jar WebGoat/webgoat-2023.5-SNAPSHOT.jar --server.address=13.126.140.19 --server.port=8082 &"'
 				
           		}
		    }
            	}     
    	}
	
 	 stage ('Dynamic analysis') {
            steps {
          	  sshagent(['dast-test']) {
            sh 'ssh -o  StrictHostKeyChecking=no ubuntu@13.233.206.20 "sudo docker run --rm -v /home/ubuntu:/zap/wrk/:rw -t owasp/zap2docker-stable zap-full-scan.py -t http://13.126.140.19/WebGoat -x report_xml || true" '
			
                  }      
            }       
    }
    }
}
