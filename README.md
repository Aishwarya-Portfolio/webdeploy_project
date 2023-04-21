# webdeploy_project
### Setup CI/CD with 
1. GitHub, 
2. Jenkins, 
3. Maven & 
4. Tomcat. 

* Setup Jenkins 
* Setup & Configure Maven , Git. 
* Setup Tomcat Server. 
* Integrating GitHub,Maven ,Tomcat Server with Jenkins 
* Create a CI and CD Job. 
* Test the Deployment.

### Resources to Setup CI and CD pipeline. 
* Free Tier AWS account. 
* GitHub account (for source code and documentation). 
* MobaXterm – enhanced terminal for windows with X11 Server tabbed SSH clients, network tool and much more. 
* Git – local version control system. 

![image](https://user-images.githubusercontent.com/91592578/233707608-5a42702e-48c4-4814-bbd3-ef962adcf0d6.png)

### Steps:
1. Create 1 EC2 instance - Amazon linux.
2. Connect to mobaxterm using ppk file.
3. Install jenkins - 
  * Reference: https://pkg.jenkins.io/redhat-stable/
  * Known Issue - unable to install amazon-linux-extras which will install java and jenkins.
  
 ![image](https://user-images.githubusercontent.com/91592578/233709028-fe315ec8-5325-4f68-b371-71990bb5c0ed.png)
 
  * Resolution: Follow below commands -
````
sudo yum install -y java-11-amazon-corretto-devel git
````
![image](https://user-images.githubusercontent.com/91592578/233709217-5dc4fd95-52e6-4baf-976f-9a028704960a.png)

````
sudo wget -O /etc/yum.repos.d/jenkins.repo \
sudo rpm --import 
sudo yum install -y jenkins
````
![image](https://user-images.githubusercontent.com/91592578/233711247-c36d4aff-beb9-4164-ba0c-f76027520396.png)

````
sudo systemctl start jenkins	
sudo systemctl status jenkins
````
4. Configure Jenkins and Setup Job - 
* Access Jenkins on Port 8080 and install all the suggested plugins. 
* Create admin user and login to setup Jenkins job.
* Create first Jenkins job - Freestyle
* Just added a script to echo "Hello World"

![image](https://user-images.githubusercontent.com/91592578/233711580-c3ffa379-59c6-4da3-a604-e311aedd784b.png)

5. Configure Git - 
* Install git in ec2 - jenkins instance.
> It might be installed already.

* Install github plugin on jenkins GUI.
> Go to plugin manager and find - git, which must be already installed during Jenkins configuration.

* Configure git on jenkins GUI.
> Navigate to Global tool configuration
![image](https://user-images.githubusercontent.com/91592578/233712630-036ec922-07f1-4018-9f51-3a292fc613ed.png)

6. Create Jenkins job -
* Run jenkins job to pull the github code - https://github.com/Aishwarya-Portfolio/hello-world.git

<img width="700" alt="image" src="https://user-images.githubusercontent.com/91592578/233713283-67fc7fd1-64eb-41f4-8a71-75441207663e.png">

* Build successfully - 

![image](https://user-images.githubusercontent.com/91592578/233713617-df477ff6-1c99-4ccf-8543-d662bae909e9.png)

> The code has been pulled to the jenkins workspace.

![image](https://user-images.githubusercontent.com/91592578/233713744-0f90064f-e5e7-4577-b837-3b009c47f152.png)

## INTEGRATE MAVEN WITH JENKINS
1. Setup Maven on Jenkins Server.
````
sudo wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
sudo sed -i s/\$releasever/6/g /etc/yum.repos.d/epel-apache-maven.repo
sudo yum install -y apache-maven
mvn --version
````
![image](https://user-images.githubusercontent.com/91592578/233714153-8ab8efe6-241d-4bae-b98c-ab61622509f0.png)


> Maven home: /usr/share/apache-maven

> Java home: /usr/lib/jvm/java-11-amazon-corretto.x86_64

2. Setup Environment Variables.
* JAVA_HOME, M2, M2_HOME 
> [ec2-user@ip-172-31-63-227 ~]$ sudo nano .bash_profile

````
# .bash_profile
		
# Get the aliases and functions
if [ -f ~/.bashrc ];
then
  . ~/.bashrc
fi
		
M2_HOME=/usr/share/apache-maven
M2=/usr/share/apache-maven/bin
JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64
		
# User specific environment and startup programs
PATH=$PATH:$HOME/bin:$JAVA_HOME:$M2_HOME:$M2
export PATH
````

> [ec2-user@ip-172-31-63-227 ~]$ echo $PATH

> /home/ec2-user/.local/bin:/home/ec2-user/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/ec2-user/bin:/usr/lib/jvm/java-11-amazon-corretto.x86_64:/usr/share/apache-maven:/usr/share/apache-maven/bin

3. Install Maven Plugin.

![image](https://user-images.githubusercontent.com/91592578/233715005-6f7d34e0-3d59-41ee-83db-d8d7bb561634.png)

4. Configure Maven and Java.

![image](https://user-images.githubusercontent.com/91592578/233715107-1719abc2-64d5-4fe3-bd65-3154fed15f40.png)

![image](https://user-images.githubusercontent.com/91592578/233715120-c1801833-fd55-4624-befe-a8258f07f8e3.png)

5. Now build the java project using maven.

![image](https://user-images.githubusercontent.com/91592578/233715184-c89e72d6-b06d-4d2e-a074-dd0b868297a7.png)

![image](https://user-images.githubusercontent.com/91592578/233715210-0af7a5c4-b7bf-4ac1-b834-3d8a19ea5945.png)

6. Artifact is generated.
	
![image](https://user-images.githubusercontent.com/91592578/233715253-65cdc9e0-c5a6-4e84-8808-bc292efc5f34.png)

### INTEGRATE TOMCAT IN CI/CD PIPELINE

1. Setup Tomcat Server
2. Setup a Linux EC2 Instance
3. Install Java -
````
sudo yum install -y java-11-amazon-corretto-devel git
````
4. Configure Tomcat -
* create tomcat directory -
````
cd /opt
wget https://archive.apache.org/dist/tomcat/tomcat-8/v8.5.35/bin/apache-tomcat-8.5.35.tar.gz
tar -xvzf /opt/apache-tomcat-8.5.35.tar.gz
````
* Give executing permissions to startup.sh and shutdown.sh which are under bin -
````
chmod +x /opt/apache-tomcat-8.5.35/bin/startup.sh
chmod +x /opt/apache-tomcat-8.5.35/bin/shutdown.sh
````

* Create link files for tomcat startup.sh and shutdown.sh -
````
ln -s /opt/apache-tomcat-8.5.35/bin/startup.sh /usr/local/bin/tomcatup
ln -s /opt/apache-tomcat-8.5.35/bin/shutdown.sh /usr/local/bin/tomcatdown
````

5. Start Tomcat Server -
````
tomcatup
````

6. Access Web UI on port 8080.

![image](https://user-images.githubusercontent.com/91592578/233716329-fb8bc5d2-4b77-44e0-bf70-4d9ae87b297e.png)

7. There is a access denied error on manage app - 

![image](https://user-images.githubusercontent.com/91592578/233716460-8045cdba-6d52-48a5-96ec-79a3fe7b260e.png)

* Resolution - 
````
[root@ip-172-31-2-21 opt]# find -name context.xml
./apache-tomcat-8.5.35/conf/context.xml
./apache-tomcat-8.5.35/webapps/host-manager/META-INF/context.xml
./apache-tomcat-8.5.35/webapps/manager/META-INF/context.xml
````

> allow access host manager in context file.
````
nano ./apache-tomcat-8.5.35/webapps/host-manager/META-INF/context.xml
````
> allow access manager in context file.
````
nano ./apache-tomcat-8.5.35/webapps/manager/META-INF/context.xml
````

![image](https://user-images.githubusercontent.com/91592578/233716823-e8ac8c23-9ce9-4559-b396-c2a116fd90b7.png)
![image](https://user-images.githubusercontent.com/91592578/233716840-99f959b5-f750-46ca-8027-5a1c24b887dd.png)

> Create tomcat user.
````
[root@ip-172-31-2-21 apache-tomcat-8.5.35]# cd conf/
````
![image](https://user-images.githubusercontent.com/91592578/233716972-9406af90-52ca-4e27-8d17-8e2587aea5ee.png)

> Restart Tomcat.
````
tomcatdown
tomcatup
````

* Now again try to access manage app and login with admin user credentials.

8. Integrate Tomcat with Jenkins.
* Install plug- in “deploy-to-container”

![image](https://user-images.githubusercontent.com/91592578/233717521-4552df90-b23b-48bb-a0b9-ef1b7270561a.png)

* Configure Tomcat Server with Credentials.

![image](https://user-images.githubusercontent.com/91592578/233717595-ba3bd210-c565-40fd-bf32-feee1c63a585.png)

9. Now Modify the existing job and run it to build through maven and deploy on tomcat.
* Add a post build step - deploy to container - 
![image](https://user-images.githubusercontent.com/91592578/233717798-90611294-7a89-47ab-8b8a-5f1b95bfece9.png)

* Deployment will be successful - 

<img width="700" alt="image" src="https://user-images.githubusercontent.com/91592578/233718232-056c7105-3316-497e-b1d0-415323f4a4b2.png">


