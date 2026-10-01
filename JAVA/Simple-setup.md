## Copy the file to server
```Bash
scp -P 2222 /Users/karthikeyana/workspace/KumarBrooms/backend/build/libs/kumarbrooms.jar astraval@103.194.228.52:/var/www/kumarbrooms/
```
## Test Running
```Bash
java -jar /var/www/kumarbrooms/kumarbrooms.jar
```

## Production Best Practice: Run as a Systemd Service
Create a service file:

```Bash
sudo nano /etc/systemd/system/kumarbrooms.service
```
Paste the following configuration (adjust the user or java path if needed):

```Ini, TOML
[Unit]
Description=KumarBrooms Spring Boot Application
After=network.target

[Service]
User=astraval
ExecStart=/usr/bin/java -jar /var/www/kumarbrooms/kumarbrooms.jar
SuccessExitStatus=143
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```
Save and exit (Ctrl + O, then Enter, then Ctrl + X).

Enable and start the service:

```Bash
sudo systemctl daemon-reload
sudo systemctl enable kumarbrooms
sudo systemctl start kumarbrooms
```
Check its status at any time:

```Bash
sudo systemctl status kumarbrooms
```

## Edit the Apache Configuration File
Open your Apache configuration file on the server:

```Bash
sudo nano /etc/apache2/sites-available/kumarbrooms.conf
```
## Update the ServerName
Change the ServerName line from your IP address to your new domain name:

```Apache
<VirtualHost *:80>
    ServerName kumarbrooms.astraval.com

    ProxyPreserveHost On
    ProxyRequests Off

    <Proxy *>
        Order deny,allow
        Allow from all
    </Proxy>

    ProxyPass / http://localhost:8080/
    ProxyPassReverse / http://localhost:8080/

    ErrorLog ${APACHE_LOG_DIR}/kumarbrooms_error.log
    CustomLog ${APACHE_LOG_DIR}/kumarbrooms_access.log combined
</VirtualHost>
```

Save and exit (Ctrl + O, then Enter, then Ctrl + X).

## Test and Restart Apache
Run these commands to apply the changes:

```Bash
sudo apache2ctl configtest
sudo systemctl restart apache2
```
