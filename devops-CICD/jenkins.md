环境准备
jdk  
git
maven

如果是在linux,可以使用whereis java  
whereis mvn   
whereis git  

jenkins安装  
```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
```
