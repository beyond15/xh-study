环境准备
jdk  
git
maven

如果是在linux,可以使用whereis java  
whereis mvn   
whereis git  

### jenkins安装  
```
sudo wget -O /etc/yum.repos.d/jenkins.repo http://pkg.jenkins-ci.org/redhat/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
```
启动
```
service jenkins start
```
访问,输入密码
```
vi /var/lib/jenkins/secrets/initialAdminPassword
```
### jenkins工作插件配置  
配置git、jdk、maven

### 配置项目
参数化构建过程

jar_path :本意是准备项目打包后的jar位置，其实这里是Jenkins工作空间  
spring_profile：这个是读取配置文件前缀，比如dev，test，prod  
jar_name：jar包名称  
project_name：项目名称  

git 的配置  

### 配置build
```
mvn clean install -Dmaven.test.skip=true
echo $spring_profile $jar_path $jar_name
cd /usr/local/shell/
./stop.sh $jar_name
echo "Execute shell Finish"
./startup.sh $spring_profile $jar_path $jar_name $project_name
```

stop.sh
```
jar_name=${1}
echo "Stopping" ${jar_name}
pid=`ps -ef | grep ${jar_name} | grep -v grep | awk '{print $2}'`
if [ -n "$pid" ]
then
   echo "kill -9 的pid:" $pid
   kill -9 $pid
fi
```
startup.sh
```
spring_profile=${1}
jar_path=${2}
jar_name=${3}
project_name=${4}
cd ${jar_path}/${project_name}/target/
echo ${jar_path}/${project_name}/target/
echo nohup java -jar ${jar_name} &
BUILD_ID=dontKillMe nohup java -jar ${jar_name} --spring.profiles.active=${spring_profile} &
```

里面的坑挺多的，加油。

