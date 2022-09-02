pipeline{
    agent any
	tools { 
      maven 'maven123' 
      jdk 'javaman' 
    }
	
    stages{

	   stage('Clone repo') {
        steps {
            dir("cicd"){
                git url: 'https://github.com/pponnusw1/aws-microservices.git'
                }
            }
        }
	   stage('Build Artifact') {
        steps {
            dir("cicd/03-currency-exchange-service-h2/"){
               sh 'mvn clean package' 
              }
			dir("cicd/05-currency-conversion-service/"){
               sh 'mvn clean package' 
              }  
            }
         }
		
		
       stage('Building Docker Image') {
	   
		   steps {

		       sh 'docker system prune -a -f || true'

		       dir ('cicd/03-currency-exchange-service-h2/') {
				  sh 'docker  build -t pponnusw/currency-exchange-service:1.0 .'
			   }

			   dir ('cicd/05-currency-conversion-service/') {
				  sh 'docker build -t pponnusw/currency-conversion-service:1.0 .'
			   }

		   }
        }

	   stage('Push Docker Images to Docker hub repository'){
		   steps {

				  sh 'docker login -u pponnusw -p indians123'
				  sh 'docker push pponnusw/currency-exchange-service:1.0'
				  sh 'docker push pponnusw/currency-conversion-service:1.0'
		   }
	    }

	    stage('Deploy docker images to EC2 instances'){
		   steps {
                script{
					def remote = [:]
					remote.user = 'ubuntu'
					remote.host = 'ec2-54-82-24-21.compute-1.amazonaws.com'
					remote.name = 'Microservices(Docker)'
					remote.identityFile = '/opt/aws-key.ppk'
					remote.allowAnyHosts = 'true'
					echo 'successfully connected the release environment'
					sshCommand remote: remote, command: 'sudo docker system prune -a -f || true'
					
					// below code will pull images from docker hub
					sshCommand remote: remote, command: 'sudo docker login -u pponnusw -p indians123'
					sshCommand remote: remote, command: 'sudo docker pull pponnusw/currency-exchange-service:1.0'
					sshCommand remote: remote, command: 'sudo docker pull pponnusw/currency-conversion-service:1.0'

					// give permission to docker socks
					sshCommand remote: remote, command: 'sudo chmod 777 /var/run/docker.sock'
					sshCommand remote: remote, command: 'sudo chmod 777 /home/ubuntu/.docker/config.json'

					// deploy the code
					sshCommand remote: remote, command: 'sudo docker network create network-exchange || true'
					sshCommand remote: remote, command: 'sudo docker run --publish 8000:8000 --network network-exchange --name=currency-exchange-service pponnusw/currency-exchange-service:1.0'
					sshCommand remote: remote, command: 'sudo docker run --publish 8100:8100 --network network-exchange --env CURRENCY_EXCHANGE_URI=http://currency-exchange-service:8000 pponnusw/currency-conversion-service:1.0'
				}

		   }
	    }

    }

}