1.Download jenkins.war file
```sh
   $ wget http://mirror.serverion.com/jenkins/war/2.243/jenkins.war
```
2.Copy jenkins.war file into tomcat webapps folder
```sh
   $ cp -rv jenkins.war /opt/tomcat/webapps
```
3.Change permission to tomcat
```sh
   $ chmod g+w /opt/tomcat
```
4.Start tomcat
```sh
   $ cd /opt/tomcat/
   $ ./bin/startup.sh
```
