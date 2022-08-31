pipeline{
    agent none
    stages{
	
	   stage('Clone repo') {
        steps {
            sh 'mkdir -p cicd'
            dir("cicd"){
                git url: 'https://github.com/pponnusw1/aws-microservices.git'
                }
            }
        }

       stage('Building Docker Image') {
	   
	       agent any
	   
		   steps {

		       sh 'docker system prune -a -f || true'

		       dir ('cicd/03-aws-currency-exchange-service-h2/') {
				  sh 'docker  build -t pponnusw/currency-exchange-service:1.0 .'
			   }

			   dir ('cicd/05-aws-currency-conversion-service/') {
				  sh 'docker build -t pponnusw/currency-conversion-service:1.0 .'
			   }

		   }
        }

	   stage('Push Docker Images to Docker hub repository'){
		   steps {

				  //sh 'docker login -u pponnusw -p indians123'
				  sh 'docker push pponnusw/currency-exchange-service:1.0'
				  sh 'docker push pponnusw/currency-conversion-service:1.0'
		   }
	    }

	    stage('Deploy docker images to EC2 instances'){
		   steps {
                script{
					def remote = [:]
					remote.user = 'ubuntu'
					remote.host = 'ec2-18-208-183-13.compute-1.amazonaws.com'
					remote.name = 'Microservices(Docker)'
					remote.identityFile = '/opt/my-key-pair.ppk'
					remote.allowAnyHosts = 'true'
					echo 'successfully connected the release environment'
					// delete the existing containers and images if running
					sshCommand remote: remote, command: 'sudo docker stop mlservice_mlservice_1 || true'
					sshCommand remote: remote, command: 'sudo docker rm  mlservice_mlservice_1 || true'
					sshCommand remote: remote, command: 'sudo docker stop mlservice_testtoolservice_1 || true'
					sshCommand remote: remote, command: 'sudo docker rm  mlservice_testtoolservice_1 || true'
					sshCommand remote: remote, command: 'sudo docker stop mlservice_nginx_1 || true'
					sshCommand remote: remote, command: 'sudo docker rm  mlservice_nginx_1 || true'
					sshCommand remote: remote, command: 'sudo docker stop postgres || true'
					sshCommand remote: remote, command: 'sudo docker rm  postgres || true'
					sshCommand remote: remote, command: 'sudo docker stop redis || true'
					sshCommand remote: remote, command: 'sudo docker rm  redis || true'
					//sshCommand remote: remote, command: 'sudo docker system prune -a -f || true'
					//sshCommand remote: remote, command: 'sudo [  "$(docker volume ls | grep cert_volume)" ] && docker volume rm cert_volume || true'
					// below code will pull images from docker hub
					sshCommand remote: remote, command: 'sudo docker login -u rdineshinfo -p DiNeSh@321'
					sshCommand remote: remote, command: 'sudo docker pull rdineshinfo/mlservicerepo:mlservice'
					sshCommand remote: remote, command: 'sudo docker pull rdineshinfo/mlservicerepo:testtoolservice'
					sshCommand remote: remote, command: 'sudo docker pull rdineshinfo/mlservicerepo:nginx-gateway'
					sshCommand remote: remote, command: 'sudo rm -rf /opt/djangoapp/mlservice'
					sshCommand remote: remote, command: 'sudo rm -rf /opt/djangoapp'
					sshCommand remote: remote, command: 'sudo mkdir /opt/djangoapp/  || true'
					sshCommand remote: remote, command: 'sudo chmod 777 -R /opt/djangoapp'
					sshPut remote: remote, from: 'mlservice', into: '/opt/djangoapp'
					sshCommand remote: remote, command: 'sudo chmod 777 -R /opt/djangoapp/mlservice'
					// this below commands are used for creating docker volumes
					// Note: the folder /opt/models should exist on host before creating volume
					// below code is for making directories
					sshCommand remote: remote, command: 'sudo mkdir -p /opt/models'  // backup of models from redis in host location
					sshCommand remote: remote, command: 'sudo chmod 777 -R /opt/models'
					sshCommand remote: remote, command: 'sudo mkdir -p /opt/jobs'   // backup of postres in host location
					sshCommand remote: remote, command: 'sudo chmod 777 -R /opt/jobs'
					sshCommand remote: remote, command: 'sudo mkdir -p /opt/certs'
					sshCommand remote: remote, command: 'sudo chmod 777 -R /opt/certs'
					sshCommand remote: remote, command: 'sudo docker volume create --name cert_volume  --opt type=none --opt device=/opt/certs --opt o=bind || true'
					// give permission to docker socks
					sshCommand remote: remote, command: 'sudo chmod 777 /var/run/docker.sock'
					sshCommand remote: remote, command: 'sudo chmod 777 /home/ubuntu/.docker/config.json'
					//sshCommand remote: remote, command: 'sudo [ ! "$(docker volume ls | grep redisdata)" ] && docker volume create --name redisdata  || true'
					//sshCommand remote: remote, command: 'sudo [ ! "$(docker volume ls | grep postgres_data)" ] && docker volume create --name postgres_data || true'
					// This command is assumed to be always true because docker-compose is already installed in target machine. If
					// docker-compose is not installed in target machine  this command will return false and hence dployment will fail.
					// deploy the code
					sshCommand remote: remote, command: 'sudo docker-compose -f /opt/djangoapp/mlservice/docker-compose.yml --env-file /opt/djangoapp/mlservice/prod.env up --detach --build'
					sshCommand remote: remote, command: 'sudo docker-compose -f /opt/djangoapp/mlservice/docker-compose.yml exec -T mlservice python /opt/djangoapp/mlservice/manage.py migrate --run-syncdb'
					sshCommand remote: remote, command: 'sudo docker-compose -f /opt/djangoapp/mlservice/docker-compose.yml exec -e  DJANGO_SUPERUSER_PASSWORD=password -T mlservice python /opt/djangoapp/mlservice/manage.py createsuperuser --username userforjwt  --email prabhakaran-p@hcl.com --noinput  || true'
					sshCommand remote: remote, command: 'sudo docker-compose -f /opt/djangoapp/mlservice/docker-compose.yml exec -e  DJANGO_SUPERUSER_PASSWORD=password -T mlservice python /opt/djangoapp/mlservice/manage.py createsuperuser --username admin  --email prabhakaran-p@hcl.com --noinput  || true'
				}

		   }
	    }

    }

}