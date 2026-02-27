# 📌 Lab 5 – EC2 Application Connected to Amazon RDS (MySQL)

## 👨‍🎓 Student Details
**Name:** Saad Dalvi  
**Course:** Cloud Computing Lab  
**Application:** Spring Boot RealWorld API  
**Database:** Amazon RDS MySQL  
**EC2 Public URL:** http://43.205.199.5  

---

# 📖 Project Overview

This project demonstrates deployment of the Spring Boot RealWorld API on an AWS EC2 instance and secure integration with Amazon RDS MySQL.

The application performs full CRUD (Create, Read, Update, Delete) operations using the RDS database.

Objectives of this lab:
- Deploy application on EC2
- Connect it to managed AWS RDS
- Demonstrate all CRUD operations
- Follow proper AWS security best practices

---

# 🏗 Architecture

User (curl/Postman/Browser)  
↓  
Internet  
↓  
EC2 Instance (Ubuntu 24.04)  
↓  
Nginx (Port 80 Reverse Proxy)  
↓  
Spring Boot Application (Port 8080)  
↓  
Amazon RDS MySQL  

---

# 🔐 Security Configuration

## EC2 Security Group
- Port 22 (SSH) → My IP only  
- Port 80 (HTTP) → 0.0.0.0/0  
- Port 8080 → Not publicly exposed  

## RDS Security Group
- Port 3306 (MySQL)  
- Source → EC2 Security Group only  
- Public Access → Disabled  

This ensures the database is not accessible from the internet.

---

# ⚙ Deployment Steps

## 1️⃣ Launch EC2
- OS: Ubuntu 24.04
- Instance Type: t3.small
- Key pair created and used for SSH

---

## 2️⃣ Install Dependencies

```bash
sudo apt update
sudo apt install openjdk-17-jdk
sudo apt install nginx
```

---

## 3️⃣ Clone and Build Application

```bash
git clone https://github.com/gothinkster/spring-boot-realworld-example-app.git
cd spring-boot-realworld-example-app
./gradlew build -x test -x spotlessCheck -x spotlessJava
```

---

## 4️⃣ Configure Amazon RDS

- Engine: MySQL  
- Database Name: realworlddb  
- Port: 3306  
- Public Access: Disabled  
- Inbound Rule: Allow MySQL from EC2 Security Group only  

---

## 5️⃣ Configure Database Connection

`application.properties`

```properties
spring.datasource.url=jdbc:mysql://<RDS-ENDPOINT>:3306/realworlddb
spring.datasource.username=admin
spring.datasource.password=${DB_PASSWORD}
spring.jpa.hibernate.ddl-auto=validate
```

Set environment variable:

```bash
export DB_PASSWORD=yourpassword
```

---

## 6️⃣ Configure Nginx Reverse Proxy

```nginx
server {
    listen 80;
    server_name _;

    location / {
        proxy_pass http://127.0.0.1:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

Restart Nginx:

```bash
sudo systemctl restart nginx
```

---

## 7️⃣ Configure systemd Service

```bash
sudo nano /etc/systemd/system/realworld.service
sudo systemctl daemon-reload
sudo systemctl enable realworld
sudo systemctl start realworld
```

This ensures the application starts automatically on reboot.

---

# 🗄 Database Schema

Tables used:
- users
- articles
- tags
- article_tags

Relationship:
- articles.author_id → users.id (Foreign Key)

---

# 🔁 CRUD Operations Demonstrated

## ✅ Create
- User registration
- Article creation with tags

## ✅ Read
- User login
- Fetch articles
- Verified using:
  ```
  SELECT * FROM users;
  SELECT * FROM articles;
  ```

## ✅ Update
- Updated user bio using:
  ```
  PUT /user
  ```
- Verified updated record in RDS.

## ✅ Delete
- Deleted article using:
  ```
  DELETE /articles/{slug}
  ```
- Confirmed removal from database.

Application Access

Application hosted at:

http://43.205.199.5/articles

---

# ✅ Conclusion

The Spring Boot RealWorld API was successfully deployed on EC2 and securely connected to Amazon RDS MySQL. All CRUD operations were demonstrated and verified through the EC2-hosted application.

Proper AWS security best practices were followed by restricting database access to the EC2 Security Group only.
