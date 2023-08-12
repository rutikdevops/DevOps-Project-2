# DevOps-Project-2
- In this project, I build and deploy my application on Docker Container with the help of Ansible.
![Commit Code](https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/2a2802e7-8dad-4d7a-ab47-9043f1aee546)
<br></br>


Once the latest code is available on the Git repository, Jenkins is capable of fetching the updated code, initiating a build using Maven, creating Docker images, and subsequently deploying these Docker containers onto the Docker host.

In that case, we used Jenkins as a tool for building and  used docker for deploying applications.

So in this case Jenkins is going to take the code from GitHub and build artifacts and copy those artifacts onto Ansible server.

Now it is Ansible’s task to create images and deploy the containers.Ansible is going to take the artifact and with the help of Docker file it creates a Docker image.

This Docker image, we can commit it into the Docker hub because Docker Hub is a repository to store Docker images.

Now whenever we execute any Ansible playbook to deploy a container, this Docker host communicates with the Docker hub and pull the image whatever we mentioned in our playbook and create a container out of it.



# Project Blog link :-
# Project Steps :-
- Create 4 ec2 instance name as:- (AWS Linux-2, t2 micro)
1. Jenkins-Server
2. Ansible-Server
3. Docker-Server
4. Tomcat-Server

# 1. Install  and Configure the Jenkins :-
```bash
ec2-user
sudo su
yum update -y
hostnamectl set-hostname jenkins
bash
```
- Java installation on Jenkins server:-
```bash
yum install java* -y
java --version
alternatives --config java               ## Using this command you can choose any version of Java
```

- Jenkins installation on Jenkins server:-
```bash
wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key
yum install jenkins -y
systemctl enable jenkins.service
systemctl start jenkins.service
systemctl status jenkins.service
```
- Copy public ip of jenkins server and paste it in new tab with port no.8080
<img width="576" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/75d4392c-c663-441e-baa8-39c11af26ead">

- copy this path and paste in terminal with "cat" command
```bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```
- Now copy & paste this passwd in jenkins. so, jenkins is ready.
<br></br>


# 2. Install and Configure the Maven :-
```bash
sudo su
cd ~
pwd
cd /opt
wget https://dlcdn.apache.org/maven/maven-3/3.9.4/binaries/apache-maven-3.9.4-bin.tar.gz
tar -xvzf apache-maven-3.9.4-bin.tar.gz
mv apache-maven-3.9.4 maven
cd maven/
cd bin/
./mvn -v
```
```bash
cd ~
ll -a
vim .bash_profile
```
- In the vi editor add this path :-
<img width="373" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/9db87c99-c213-4f9a-bec4-9f92591ea521">


```bash
M2_HOME=/opt/maven
M2=/opt/maven/bin
JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto.x86_64
PATH=$PATH:$HOME/bin:$JAVA_HOME:$M2_HOME:$M2
```
- Find java path using this command:-
```bash
find / -name java-11*
```
- Showing maven & java path :-
```bash
source .bash_profile
echo $PATH
```
<br></br>


# 3. Install Maven Plugin and Configure Jenkins for Maven :-
- Dashboard-> Manage Jenkins-> Pluins-> Available Plugins-> Maven Integration(Install this)
- Manage Jenkins-> Tools-> JDK
![image](https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/652d9f27-0f0f-48c0-b921-4bfa415adafd)
<br></br>

- Manage Jenkins-> Tools-> Maven
![image](https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/c5cb7642-bb7c-424a-b2a4-4af5cc37b517)
<br></br>

- Dashboard-> Manage Jenkins-> Pluins-> Installed Plugins->github
- GitHub Branch Source Plugin = Disable
- GitHub plugin = Enable
<img width="943" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/6f52861f-f1f8-41eb-af4a-0f694dd8b2ec">
<br></br>

- git installation on Jenkins server:-
```bash
yum install git -y
```
- Jenkins-> New Item-> Maven Project
<img width="896" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/b08d1fce-85ac-4d0c-ac03-e15473e693d7">
<br></br>

- In this job do this:-
<img width="327" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/f87c1cb7-e1dc-4236-af4c-0c180c77f1a7">
<br></br>

![image](https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/0dce3d42-4c3c-4aeb-96cf-39fbc033bd58)
<br></br>

<img width="342" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/edebe965-eb60-40c5-b45c-9984426e7019">
<br></br>

- Maven Build is successful:-
<img width="960" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/56b480b5-9ab0-4bdf-a488-5b0a7d123d31">
<br></br>





# 4. Ansible Server setup and Ansible Installation:-
```bash
ec2-user
sudo su
yum update -y
hostnamectl set-hostname jenkins
bash
```
- add user
```bash
useradd ansadmin
passwd ansadmin      ## enter passwd 2 times
visudo               ## In vi editor go to end of the file = press shift+g without press i
ansadmin  ALL=(ALL)       NOPASSWD: ALL      ## add this in editor
```
<img width="615" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/d1a6bca1-5cf1-4b11-a670-229be1b5bf6b">

```bash
vi /etc/ssh/sshd_config      ##Do this changes in vi editor:- ** #PermitRootLogin yes (remove #) ** PasswordAuthentication no (replace no to yes)
```

- Now run this command:-
```bash
systemctl restart sshd
```

- Now switch to created user:-
```bash
sudo su - ansadmin
```

```bash
yum install ansible -y
amazon-linux-extras install ansible2 -y
ansible --version
```
- Go to ansible server and type command:-
```bash
ssh-keygen      ##(and press enter 2 to 3 times)
```
<br></br>


# 5. Integrate Ansible with Jenkins:- 
- Jenkins-> Manage Jenkins-> System-> Publish over SSH

![image](https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/f423a8e3-c8d0-41f5-8434-d0a6d52c6c4e)
<br></br>

- Go to Ansible
```bash
cd /opt
ls
mkdir docker
chown ansadmin:ansadmin docker    ## Using this ansadmin is owner of docker directory
ls
cd docker/
ls
```

- Jenkins-> New Project-> Do this mentioned in Img-> Build Now
![image](https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/6498d30f-22ff-493c-b190-4034696b8262)

- Now, webapp.war file is involved in docker directory
<br></br>



# 6. Install and Configure Docker on Ansible Server
```bash
yum install docker -y
```
```bash
id ansadmin
usermod -aG docker ansadmin     ## provide all the access to ansadmin on the docker
```
```bash
service docker start
service docker status
```
```bash
su - ansadmin
cd /opt/docker/
vi dockerfile     ## add below commande in dockerfile
FROM tomcat:latest
RUN cp -R /usr/local/tomcat/webapps.dist/* /usr/local/tomcat/webapps
COPY ./*.war /usr/local/tomcat/webapps
```
<br></br>



# 7. Create Ansible Playbook to create Docker Image and Push that Image to DockerHub
- In Ansivle Open host file
 ```bash
 vi /etc/ansible/hosts

[Ansible]                       ## type this in editor
(paste public Ip here)
 ```
```bash
ssh-copy-id (paste here ansible private ip)
## enter passwd 2 times
```

- Create Ansible playbook to create the docker image and push image to dockerhub
```bash
vi regapp.yml   ## paste below cmnds in editor
---
- hosts: ansible
  tasks:
  - name: create docker image
    command: docker build -t regapp:latest .
    args:
      chdir: /opt/docker

  - name: create tag to push image onto dockerhub
    command: docker tag regapp:latest rutikdevops/regapp:latest

  - name: push docker image
    command: docker push rutikdevops/regapp:latest
```


- check indentation of playbook
```bash
ansible-playbook regapp.yml --check
```

```bash
docker images
docker login
```
<br></br>



# 8. Update Jenkins Job to use the Ansible Playbook
- Jenkins-> Project-> Configure

<img width="614" alt="image" src="https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/c291a265-49e7-4f73-a946-840f5c64acd0">
<br></br>
- Now, your image is pushed in DockerHub




# 9. Webapp deploy to container
- Do passwordless authentication between Ansible & Docker
- Install apache on docker server
```bash
yum install httpd -y
systemctl enable httpd
systemctl start httpd
systemctl status httpd
```

- In Ansible Open host file
 ```bash
 vi /etc/ansible/hosts

[docker]                       ## type this in editor
(paste public Ip here)
 ```
```bash
ssh-copy-id (paste here docker private ip)
## enter passwd 2 times
```

- Create Ansible playbook to deploy webapp on server:-
```bash
vi deploy_regapp.yml   ## paste below cmnds in editor
---
- hosts: docker
  tasks:
    - name: stop existing container
      command: docker stop regapp-server
      ignore_errors: yes
    - name: remove the container
      command: docker rm regapp-server
      ignore_errors: yes
    - name: remove image
      command: docker rmi rutikdevops/regapp:latest
      ignore_errors: yes
    - name: create container
      command: docker run -d --name regapp-server -p 8082:8080 rutikdevops/regapp:latest
```

- Now, regapp sussessfully host on server
  
















