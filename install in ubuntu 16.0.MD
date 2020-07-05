Step 1: Install Java
```sh
$ sudo apt-get update
$ sudo apt-get install default-jdk
```
Step 2: Create Tomcat User
```sh
$ sudo groupadd tomcat
$ sudo useradd -s /bin/false -g tomcat -d /opt/tomcat tomcat
```
Step 3: Install Tomcat
```sh
$ cd /tmp
$ curl -O http://apachemirror.wuchna.com/tomcat/tomcat-8/v8.5.56/bin/apache-tomcat-8.5.56.tar.gz
$ sudo mkdir /opt/tomcat
$ sudo tar xzvf apache-tomcat-8*tar.gz -C /opt/tomcat --strip-components=1
```
Step 4: Update Permissions
```sh
$ cd /opt/tomcat
$ sudo chgrp -R tomcat /opt/tomcat
$ sudo chmod -R g+r conf
$ sudo chmod g+x conf
$ sudo chown -R tomcat webapps/ work/ temp/ logs/
```
Step 5: Create a systemd Service File
```sh
$ sudo update-java-alternatives -l

     Output
     java-1.8.0-openjdk-amd64       1081       /usr/lib/jvm/java-1.8.0-openjdk-amd64

$ sudo vi /etc/systemd/system/tomcat.service
```
Paste the following contents into your service file. Modify the value of JAVA_HOME if necessary to match the value you found on your system. You may also want to modify the memory allocation settings that are specified in CATALINA_OPTS:
```sh
[Unit]
Description=Apache Tomcat Web Application Container
After=network.target

[Service]
Type=forking

Environment=JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-amd64/jre
Environment=CATALINA_PID=/opt/tomcat/temp/tomcat.pid
Environment=CATALINA_HOME=/opt/tomcat
Environment=CATALINA_BASE=/opt/tomcat
Environment='CATALINA_OPTS=-Xms512M -Xmx1024M -server -XX:+UseParallelGC'
Environment='JAVA_OPTS=-Djava.awt.headless=true -Djava.security.egd=file:/dev/./urandom'

ExecStart=/opt/tomcat/bin/startup.sh
ExecStop=/opt/tomcat/bin/shutdown.sh

User=tomcat
Group=tomcat
UMask=0007
RestartSec=10
Restart=always

[Install]
WantedBy=multi-user.target
```
Next, reload the systemd daemon so that it knows about our service file:
```sh
$ sudo systemctl daemon-reload
$ sudo systemctl start tomcat
$ sudo systemctl status tomcat
```
Step 6: Adjust the Firewall and Test the Tomcat Server
```sh
$ sudo ufw allow 8080
```
With the firewall modified, you can access the default splash page by going to your domain or IP address followed by :8080 in a web browser:

Open in web browser
http://server_domain_or_IP:8080
```sh
$ sudo systemctl enable tomcat
```
Step 7: Configure Tomcat Web Management Interface
```sh
$ sudo vi /opt/tomcat/conf/tomcat-users.xml
```
```sh
<?xml version='1.0' encoding='utf-8'?>
<tomcat-users>
    <role rolename="manager-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-jmx"/>
    <role rolename="manager-jmx"/>
    <role rolename="admin-gui"/>
    <role rolename="admin-script"/>
    <user username="edureka" password="edureka" roles="manager-gui,manager-script,manager-jmx,manager-status,admin-gui,admin-script"/>
</tomcat-users>
```
For the Manager app, type:
```sh
$ sudo vi /opt/tomcat/webapps/manager/META-INF/context.xml
```
For the Host Manager app, type:
```sh
$ sudo vi /opt/tomcat/webapps/host-manager/META-INF/context.xml
```
Inside, comment out the IP address restriction to allow connections from anywhere. Alternatively, if you would like to allow access only to connections coming from your own IP address, you can add your public IP address to the list:
```sh
context.xml files for Tomcat webapps
<Context antiResourceLocking="false" privileged="true" >
  <!--<Valve className="org.apache.catalina.valves.RemoteAddrValve"
         allow="127\.\d+\.\d+\.\d+|::1|0:0:0:0:0:0:0:1" />-->
</Context>
```
restart the Tomcat service:
```sh
$ sudo systemctl restart tomcat
```
Step 8: Access the Web Interface
```sh
http://server_domain_or_IP:8080
```
