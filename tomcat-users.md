# Modify Tomcat configuration to allow remote access to the Manager and Host Manager applications.
## Set environment variables for Tomcat (temporary for current session)
```
export CATALINA_HOME=/opt/tomcat
export PATH=$PATH:$CATALINA_HOME/bin
```

## Enable remote access by commenting out RemoteAddrValve in Manager and Host Manager
```
sed -i 's/<Valve className="org.apache.catalina.valves.RemoteAddrValve".*/<!-- & -->/' /opt/tomcat/webapps/manager/META-INF/context.xml
sed -i 's/<Valve className="org.apache.catalina.valves.RemoteAddrValve".*/<!-- & -->/' /opt/tomcat/webapps/host-manager/META-INF/context.xml
```

## Create a new tomcat user with full privileges
```
cat <<EOF > /opt/tomcat/conf/tomcat-users.xml
<tomcat-users>
    <role rolename="manager-gui"/>
    <role rolename="admin-gui"/>
    <role rolename="manager-script"/>
    <role rolename="manager-jmx"/>
    <role rolename="manager-status"/>
    <user username="admin" password="password" roles="manager-gui,admin-gui,manager-script,manager-jmx,manager-status"/>
</tomcat-users>
EOF
```

## Start Apache Tomcat
```
/opt/tomcat/bin/startup.sh
```
## Apache Tomcat 10.1.28 installed and started.
## Access it at http://<server-ip>:8080
## Use the credentials: admin / password
