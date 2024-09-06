# tomcat-setup
## Exit on error
```
set -e
```
## Define variables
```
TOMCAT_VERSION="10.1.28"
TOMCAT_TAR="apache-tomcat-$TOMCAT_VERSION.tar.gz"
TOMCAT_URL="https://downloads.apache.org/tomcat/tomcat-10/v$TOMCAT_VERSION/bin/$TOMCAT_TAR"
TOMCAT_DIR="/opt/tomcat"
JAVA_PACKAGE="java-17-openjdk"
TOMCAT_USER="tomcat"
```

## Update system
```
echo "Updating system..."
sudo dnf update -y
```

## Install Java Development Kit (JDK)
```
echo "Installing JDK..."
sudo dnf install -y $JAVA_PACKAGE
```
## Create Tomcat user
```
echo "Creating Tomcat user..."
sudo useradd -r -m -s /sbin/nologin $TOMCAT_USER
```

## Download Tomcat
```
echo "Downloading Tomcat $TOMCAT_VERSION..."
cd /tmp
wget $TOMCAT_URL
```

## Extract Tomcat
```
echo "Extracting Tomcat..."
sudo tar xzf $TOMCAT_TAR -C /opt
```

## Create a symbolic link to Tomcat
```
echo "Creating symbolic link..."
sudo ln -s /opt/apache-tomcat-$TOMCAT_VERSION $TOMCAT_DIR
```

## Set permissions
```
echo "Setting permissions..."
sudo chown -R $TOMCAT_USER: $TOMCAT_DIR
sudo chmod -R 755 $TOMCAT_DIR
```

## Create Tomcat systemd service file
```
echo "Creating Tomcat systemd service file..."
sudo tee /etc/systemd/system/tomcat.service > /dev/null <<EOL
[Unit]
Description=Apache Tomcat 10 Web Application Container
After=network.target

[Service]
Type=forking

User=$TOMCAT_USER
Group=$TOMCAT_USER

Environment="JAVA_HOME=/usr/lib/jvm/jre-openjdk"
Environment="CATALINA_HOME=$TOMCAT_DIR"
Environment="CATALINA_PID=$TOMCAT_DIR/temp/tomcat.pid"

ExecStart=$TOMCAT_DIR/bin/startup.sh
ExecStop=$TOMCAT_DIR/bin/shutdown.sh

Restart=on-failure

[Install]
WantedBy=multi-user.target
EOL
```
## Reload systemd to recognize the new service
```
echo "Reloading systemd..."
sudo systemctl daemon-reload
```

## Start and enable Tomcat service
```
echo "Starting and enabling Tomcat service..."
sudo systemctl start tomcat
sudo systemctl enable tomcat
```

## Open firewall port 8080
```
echo "Configuring firewall..."
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

## Check Tomcat status
```
echo "Checking Tomcat status..."
sudo systemctl status tomcat

echo "Tomcat $TOMCAT_VERSION installation complete. Access it at http://your_server_ip:8080"
```
