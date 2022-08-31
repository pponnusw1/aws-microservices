INstallation of docker
--------------------------
docker run -p 8080:8080 -p 50000:50000 -d -v /var/run/docker.sock:/var/run/docker.sock -v jenkins_home:/var/jenkins_home jenkins/jenkins:lts
docker exec -it --user root <container id> bash
sudo chmod 666 /var/run/docker.sock
docker exec -it <container id> bash
cat var/jenkins_home/secrets/initialAdminPassword

Installation of sonarqube;
------------------------------
 docker pull sonarqube
 docker volume create sonarqube-conf 
docker volume create sonarqube-data
docker volume create sonarqube-logs
docker volume create sonarqube-extensions
mkdir /sonarqube
ln -s /var/lib/docker/volumes/sonarqube-conf/_data /sonarqube/conf
ln -s /var/lib/docker/volumes/sonarqube-data/_data /sonarqube/data
ln -s /var/lib/docker/volumes/sonarqube-logs/_data /sonarqube/logs
ln -s /var/lib/docker/volumes/sonarqube-extensions/_data /sonarqube/extensions

docker run -d --name sonarqube -p 9000:9000 -p 9092:9092 -v sonarqube-conf:/opt/sonarqube/conf -v sonarqube-data:/opt/sonarqube/data -v sonarqube-logs:/opt/sonarqube/logs -v sonarqube-extensions:/opt/sonarqube/extensions sonarqube

EC2 security groups.
port 8080 and 9000