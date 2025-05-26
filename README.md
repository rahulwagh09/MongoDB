# 🧩 Task 1: Install & Secure MongoDB on RHEL 8 EC2 Instance

## 📌 Objective

Set up the latest stable MongoDB version on a **RHEL 8** EC2 instance, change the default port, and enable authentication with a custom MongoDB user.

---

## 🛠️ Requirements

- AWS EC2 instance with **RHEL 8**
- MongoDB 6.0 or later
- Access to modify security group and firewall (firewalld)
- MongoDB user with authentication enabled

---

## 🖥️ Step-by-Step Setup

## 🔹 1. Connect to RHEL 8 EC2 Instance

```bash
ssh -i your-key.pem ec2-user@<public-ip>
```

##  📦 2. Install MongoDB (Latest Version)

###  Create the MongoDB YUM repo:

```bash
cat <<EOF | sudo tee /etc/yum.repos.d/mongodb-org.repo
[mongodb-org-7.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/8/mongodb-org/7.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-7.0.asc
EOF
```

###  Install MongoDB:

```bash
sudo dnf install -y mongodb-org
```

###  Enable and start the service:

```bash
sudo systemctl enable mongod
sudo systemctl start mongod
```

##  🔧 3. Change Default Port (Optional: e.g., 27018)
### Edit MongoDB config:

```bash
sudo nano /etc/mongod.conf
```

###  Update the port under net:

```yaml
net:
  port: 27018
  bindIp: 0.0.0.0
```

###  Restart MongoDB:

```bash
sudo systemctl restart mongod
```

##  🧱 4. Create Admin User & Enable Authentication
###  Start the mongo shell:

```bash
mongosh --port 27018
```

###  Switch to admin DB and create user:

```javascript
use admin
db.createUser({
  user: "adminuser",
  pwd: "StrongPassword123",
  roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
})
```

###  Exit shell:

```bash
exit
```

###  Enable authentication in config:

```bash
sudo nano /etc/mongod.conf
```

###  Add or uncomment:

```yaml
security:
  authorization: enabled
```

###  Restart MongoDB:

```bash
sudo systemctl restart mongod
```

###  Test login:

```bash
mongosh --port 27018 -u "adminuser" -p --authenticationDatabase "admin"
```

##  🔓 5. Configure Firewall (firewalld) and AWS Security Group
###  🔥 On RHEL (firewalld)

```bash
sudo firewall-cmd --permanent --add-port=27018/tcp
sudo firewall-cmd --reload
```

###  🌐 On AWS EC2 Security Group
*  Go to AWS EC2 console and Find the Security Group attached to your instance

###  Add Inbound Rule:
```
Type: Custom TCP

Port: 27018

Source: Your IP or 0.0.0.0/0 (for testing only)
```

##  ✅ MongoDB Setup Summary

| Task                        | Status |
|-----------------------------|--------|
| MongoDB Installed           | ✅     |
| Port Changed                | ✅     |
| Admin User Created          | ✅     |
| Authentication Enabled      | ✅     |
| Firewall & SG Updated       | ✅     |


----------------------------------------------------------

