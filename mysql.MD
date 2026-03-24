# MySQL Setup (LocalHelp App) - Dev Notes

This is a simple setup guide for installing MySQL for LocalHelp app.

---

# 1. Install MySQL

```bash
sudo apt update
sudo apt install mysql-server -y
```

---

# 2. Start MySQL Service

```bash
sudo systemctl start mysql
sudo systemctl enable mysql
sudo systemctl status mysql
```

---

# 3. Secure MySQL (IMPORTANT)

Run this command:

```bash
sudo mysql_secure_installation
```

During setup it will ask:

* Set root password → YES
* Enter root password → `***` (set your own strong password)
* Remove anonymous users → YES
* Disallow root remote login → YES
* Remove test database → YES
* Reload privilege tables → YES

Done ✔

---

# 4. Login to MySQL

```bash
sudo mysql
```

or

```bash
mysql -u root -p
```

---

# 6. Create User

```sql
CREATE USER 'localuser'@'localhost' IDENTIFIED BY 'StrongPass@123';
GRANT ALL PRIVILEGES ON localhelp.* TO 'localuser'@'localhost';
FLUSH PRIVILEGES;
```

---

# 7. Start/Stop Commands

```bash
sudo systemctl start mysql
sudo systemctl stop mysql
sudo systemctl restart mysql
```

---

# 8. Optional Remote Access

Edit config:

```bash
sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
```

Change:

```
bind-address = 127.0.0.1
```

To:

```
bind-address = 0.0.0.0
```

Restart:

```bash
sudo systemctl restart mysql
```

---

# 9. Quick Backup

```bash
mysqldump -u root -p localhelp > backup.sql
```

Restore:

```bash
mysql -u root -p localhelp < backup.sql
```

---

# Done

MySQL is ready for LocalHelp app 🚀
