# Spring Boot based Java web application
 
This is a simple Sprint Boot based Java application that can be built using Maven. Sprint Boot dependencies are handled using the pom.xml 
at the root directory of the repository.

This is a MVC architecture based application where controller returns a page with title and message attributes to the view.

## Execute the application 
--------------------------------------------------------------------------------
choose min $ GB RAM ec2
connect to the server


setup Jenkins using below commands
~~~
Install Java

sudo apt update
sudo apt install openjdk-11-jre
Verify Java is Installed

java -version
Now, you can proceed with installing Jenkins

curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
  /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins


~~~

**Note: ** By default, Jenkins will not be accessible to the external world due to the inbound traffic restriction by AWS. Open port 8080 in the inbound traffic rules 


Login to Jenkins using the below URL:
http://:8080 [You can get the ec2-instance-public-ip-address from your AWS EC2 console page]



After you login to Jenkins, - Run the command to copy the Jenkins Admin Password - sudo cat /var/lib/jenkins/secrets/initialAdminPassword - Enter the Administrator password

Wait for the Jenkins to Install suggested plugins
and create new user

create new job
add the pipeline project
add the pipeline jenkins file from the github https://github.com/farsin28/devops_ultimate

We are using docker as Agent, for that install "Docker agent" plugin in Jenkins
(Maven and docker)


Intall Sonarqube plugin "SonarQube ScannerVersion" and install Sonarqube on server
~~~
adduser sonarqube  (login to this user)
wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
apt install unzip (as root)
unzip *   (as sonarqube)
chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
cd sonarqube-9.4.0.54424/bin/linux-x86-64/
./sonar.sh start


~~~
You can access the sonarqube using :9000 port (default username and password is admin)
Generate Token from Sonarqube via My account->security 

Goto jenkins -> Manage jenkins ->credentials ->system ->Global credentials
add this sonarqube toeken as secrete text


install docker on server (as root)
~~~
sudo apt install docker.io

usermod -aG docker jenkins
usermod -aG docker ubuntu
systemctl restart docker
~~~





Set github and docker logins via jenkins -> Manage jenkins ->credentials ->system ->Global credentials
~~~
docker tag should be docker-cred and as username password option
github tag should be github and secret text
~~~

update the Sonarqube URL in jenkins file accorng to your sonarqube URL
and username of docker in both jenkins and deployment.yml file

CD Part (local PC with minikube - ArgoCD)
--------
Now we are configuring minikube on local PC 
install Operators to setup Argo CD (refer https://operatorhub.io/operator/argocd-operator )
kubectl get pods -n operators

Deploy a basic Argo CD cluster by creating a new ArgoCD resource in the namespace where the operator is installed.

create argocd-basic.yml
~~~

apiVersion: argoproj.io/v1alpha1
kind: ArgoCD
metadata:
  name: example-argocd
  labels:
    example: basic
spec: {}
~~~
kubectl apply -f argocd-basic.yml 

 kubectl get svc

 kubectl edit svc example-argocd-server
 ( edit type from   type: ClusterIP to NodePort to run it on our local Pc browser)
 
 minikube service  list
(to get the URL to access ArgoCD dashboard via browser)


to get the password of ArgoCD, 
kubectl get secret
kubectl edit secret example-argocd-cluster
(you can see the admin password, copy it , it is a base64)
echo <put ur password> | base64
(ignore the last symbol is %)

add the the deplyment part via ArgoCD dashboard
