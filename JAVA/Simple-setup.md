## Copy the file to server
```Bash
scp -P 2222 /Users/karthikeyana/workspace/KumarBrooms/backend/build/libs/kumarbrooms.jar username@your_server_ip:/var/www/kumarbrooms/
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
