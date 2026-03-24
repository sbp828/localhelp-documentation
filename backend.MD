# Backend (LocalHelp App) - (Java Spring Boot)

---

# 1. Backend Overview

Backend service is responsible for:

* Handling REST APIs for LocalHelp frontend
* Managing help requests (create, view, accept)
* Medicine + cart + order APIs
* Connecting with MySQL database
* Business logic + validation

👉 Frontend = UI
👉 Backend = APIs + Logic
👉 Database = Storage

---

On servers, applications typically run in one of these ways:

1. Daemon / Service processes (most common for backend apps)
Apps run as long-running background processes

Managed by a service manager like:

- systemd (Linux modern standard)

- init.d (older systems)

Example:
```

👉 These are often called daemons or services: 
nginx, mysql, redis, Spring Boot app running as myapp.service
```

2. Functional / Dedicated OS users (best practice)

Applications should NOT run as root
Instead, they run under a special user account created for that app

Example:
```
These are called service accounts / functional users: 

mysql user → runs MySQL
nginx user → runs Nginx
appuser, springboot, nodeapp → custom app users
```
3. Combination (real-world standard)

Most production setups use BOTH:
bash
```
✔ systemd service starts the app
✔ app runs as a restricted functional user
✔ that user has limited permissions (security boundary)
```
# 2. Server Setup (Pre-requisites)

Install required packages:

```bash
sudo apt update
sudo apt install git openjdk-17-jdk maven unzip -y
```

---

# 3. Create Application User

We create a dedicated user to run backend service.

```bash
useradd localhelp
```

👉 This user is only for running application (not login user)

---

# 4. Standard Application Directory

```bash
mkdir /app
chown localhelp:localhelp /app
```

---

# 5. Download Backend Code

```bash
cd /app
```

```bash
git clone https://github.com/your-username/localhelp-backend.git
```

```bash
chown -R localhelp:localhelp localhelp-backend
cd localhelp-backend
```

---

# 6. Configure Application

Edit config file:

```bash
nano src/main/resources/application.properties
```

Example:

```properties
server.port=8080

spring.datasource.url=jdbc:mysql://<MYSQL-SERVER-IP>:3306/localhelp
spring.datasource.username=localuser
spring.datasource.password=StrongPass@123

spring.jpa.hibernate.ddl-auto=update
spring.jpa.show-sql=true
```

---

# 7. Build Application

Switch to app user:

```bash
su - localhelp
cd /app/localhelp-backend
```

Build jar:

```bash
mvn clean package -DskipTests
```

Jar will be created in:

```
target/*.jar
```

---

# 8. Run Application (Manual Test)

```bash
java -jar target/backend.jar
```

---

# 9. Setup Systemd Service

```bash
sudo nano /etc/systemd/system/backend.service
```

Add:

```ini
[Unit]
Description=LocalHelp Backend Service
After=network.target

[Service]
User=localhelp
WorkingDirectory=/app/localhelp-backend
ExecStart=/usr/bin/java -jar /app/localhelp-backend/target/backend.jar
Restart=always
Environment=SPRING_PROFILES_ACTIVE=prod

[Install]
WantedBy=multi-user.target
```

---

# 10. Start Backend Service

```bash
sudo systemctl daemon-reload
```

```bash
sudo systemctl start backend
```

```bash
sudo systemctl enable backend
```

---

# 11. Check Service Status

```bash
systemctl status backend
```

---

# 12. Logs

```bash
journalctl -u backend -f
```

---

# 13. Open Firewall Port

```bash
sudo ufw allow 8080
```

---

# 14. Database Schema Load (First Time Only)

Install MySQL client:

```bash
sudo apt install mysql-client -y
```

Load schema:

```bash
mysql -h <MYSQL-SERVER-IP> -u root -p < /app/localhelp-backend/schema.sql
```

---

# 15. Restart Service After Changes

```bash
sudo systemctl restart backend
```

---

# Done

Backend is running using systemd (production style setup) 🚀
