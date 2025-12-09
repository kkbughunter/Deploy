# Deploy

### Step-01 Instal java 21, Node 24, Nginx
```bash
sudo apt install openjdk-21-jdk -y
```
```bash
curl -fsSL https://deb.nodesource.com/setup_24.x | sudo -E bash -
sudo apt install -y nodejs
```
```bash
sudo apt install -y nginx
systemctl status nginx
sudo systemctl enable nginx
sudo systemctl start nginx
```
### Step-02 Clone Your `Repo`
```bash
cd /opt
git clone <REPO>
```

### Step-03 Spring Boot (Gradle) Deployment
```bash
cd /opt/your-project/
chmod +x gradlew
```
`Note: Before Build check you application.yml database config`
```bash
./gradlew clean build -x test
```
view build file
```bash
ls build/libs
```
Create a systemd service (for auto-start)
```bash
sudo nano /etc/systemd/system/yourapp.service
```
```ini
[Unit]
Description=Spring Boot App
After=network.target

[Service]
User=root
WorkingDirectory=/path/your-project
ExecStart=/usr/bin/java -jar /path/your-project/build/libs/yourapp.jar
Restart=always

[Install]
WantedBy=multi-user.target
```
Enable and Start
```bash
sudo systemctl daemon-reload
sudo systemctl enable yourapp
sudo systemctl start yourapp
```
look for log
```bash
journalctl -u yourapp.service -f
```
check using curl locally 
```bash
curl -I http://localhost:8080
```
Open backend port (if needed)
```bash
sudo ufw allow 8080
sudo ufw reload
```

### Step-03 Frontend React+Vite Deployment
```bash
cd /opt/your-project/
npm install
npm run build
```
check build file 
```bash
 ls dist/
```
Move files
```bsah
/var/www/yourapp
sudo cp -r dist/* /var/www/yourapp/
```
Configure Nginx
```bash
sudo nano /etc/nginx/sites-available/default
```
```nginx
server {
    listen 80;

    root /var/www/yourapp/;
    index index.html;

    location / {
        try_files $uri /index.html;
    }

    # Backend API
    location /api/ {
        proxy_pass http://127.0.0.1:8080/;
    }
}
```
enable your sit
```
sudo rm /etc/nginx/sites-enabled/default
sudo ln -s /etc/nginx/sites-available/yourapp /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```
reload nginx
```bash
sudo systemctl reload nginx
```

```bash
sudo ufw allow 80/tcp
sudo ufw reload
ufw status
sudo ufw allow 443/tcp
```
restart
```bash
sudo systemctl restart nginx
```
