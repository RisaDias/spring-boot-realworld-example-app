#  Lab 5 – EC2 Application Connected to Amazon RDS (MySQL)


**EC2 Public URL:** http://43.205.199.5  

# Project Overview

This project demonstrates deployment of the Spring Boot RealWorld API on an AWS EC2 instance and secure integration with Amazon RDS MySQL.

The application performs full CRUD (Create, Read, Update, Delete) operations using the RDS database.

Objectives of this lab:
- Deploy application on EC2
- Connect it to managed AWS RDS
- Demonstrate all CRUD operations
- Follow proper AWS security best practices


# Architecture

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


# Security Configuration

## EC2 Security Group
- Port 22 (SSH) : My IP only  
- Port 80 (HTTP) : 0.0.0.0/0  

## RDS Security Group
- Port 3306 (MySQL)  
- Source : EC2 Security Group only  
- Public Access : Disabled  

This ensures the database is not accessible from the internet.


# Deployment Steps

##  Launch EC2
- OS: Ubuntu 24.04
- Instance Type: t3.small
- Key pair created and used for SSH


##  Install Dependencies

```bash
sudo apt update
sudo apt install openjdk-17-jdk
sudo apt install nginx
```


##  Clone and Build Application

```bash
git clone https://github.com/gothinkster/spring-boot-realworld-example-app.git
cd spring-boot-realworld-example-app
./gradlew build -x test -x spotlessCheck -x spotlessJava
```



##  Configure Amazon RDS

- Engine: MySQL  
- Database Name: realworld-db  
- Port: 3306  
- Public Access: Disabled  
- Inbound Rule: Allow MySQL from EC2 Security Group only  

---

##  Configure Database Connection

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


# CRUD Operations Demonstrated

##  Create
- Database
- Table
- User

##  Read
- Specific User 

##  Update
- User

##  Delete
- User

---

# Conclusion

The Spring Boot RealWorld API was successfully deployed on EC2 and securely connected to Amazon RDS MySQL. All CRUD operations were demonstrated.

