# DevOps-Project-2
- In this project, I build and deploy my application on Docker Container with the help of Ansible.
![Commit Code](https://github.com/rutikdevops/DevOps-Project-2/assets/109506158/2a2802e7-8dad-4d7a-ab47-9043f1aee546)
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
  
---bash
cat /var/lib/jenkins/secrets/initialAdminPassword
```

- Now copy & paste this passwd in jenkins. so, jenkins is ready.









