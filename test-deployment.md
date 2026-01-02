# Deployment Test Checklist

## ✅ Pre-Deployment Verification

### 1. Build Test
```bash
mvn clean package -DskipTests
```
**Status**: ✅ PASSED - JAR file created successfully

### 2. Code Structure
- ✅ Entity: `User.java` with JPA annotations
- ✅ Repository: `UserRepository.java` with Spring Data JPA
- ✅ Service: `UserService.java` with business logic
- ✅ Controllers: `UserController.java` (Web) + `UserRestController.java` (API)
- ✅ Templates: HTML pages with Thymeleaf and Bootstrap
- ✅ Configuration: `application.yaml` and `application-aws.yaml`

### 3. Dependencies Check
- ✅ Spring Boot Starter Web
- ✅ Spring Boot Starter Data JPA
- ✅ Spring Boot Starter Thymeleaf
- ✅ MySQL Connector

## 🚀 Deployment Steps

### For AWS EC2:

1. **Update Database Configuration**
   ```yaml
   # In application-aws.yaml
   spring:
     datasource:
       url: jdbc:mysql://YOUR_EC2_IP:3306/userdb
       username: root
       password: YOUR_MYSQL_PASSWORD
   ```

2. **Create MySQL Database**
   ```sql
   CREATE DATABASE userdb;
   CREATE USER 'appuser'@'%' IDENTIFIED BY 'your_password';
   GRANT ALL PRIVILEGES ON userdb.* TO 'appuser'@'%';
   FLUSH PRIVILEGES;
   ```

3. **Deploy Application**
   ```bash
   # Copy JAR to EC2
   scp target/demo-0.0.1-SNAPSHOT.jar ec2-user@YOUR_EC2_IP:~/

   # SSH to EC2 and run
   ssh ec2-user@YOUR_EC2_IP
   java -jar demo-0.0.1-SNAPSHOT.jar --spring.profiles.active=aws
   ```

## 🧪 Testing Endpoints

### Web Interface Tests:
1. **Home Page**: `http://YOUR_EC2_IP:8080/`
   - Should show user list (empty initially)
   - Should have "Add User" button

2. **Add User**: `http://YOUR_EC2_IP:8080/add-user`
   - Form with Name, Email, Phone, Address fields
   - Should validate and save to database

3. **Edit User**: Click edit button on any user
   - Should populate form with existing data
   - Should update database on submit

### API Tests:
```bash
# Test connection
curl http://YOUR_EC2_IP:8080/api/hello

# Get all users
curl http://YOUR_EC2_IP:8080/api/users

# Create user
curl -X POST http://YOUR_EC2_IP:8080/api/users \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Test User",
    "email": "test@example.com",
    "phone": "1234567890",
    "address": "123 Test St"
  }'

# Get user by ID
curl http://YOUR_EC2_IP:8080/api/users/1

# Update user
curl -X PUT http://YOUR_EC2_IP:8080/api/users/1 \
  -H "Content-Type: application/json" \
  -d '{
    "name": "Updated User",
    "email": "updated@example.com",
    "phone": "0987654321",
    "address": "456 Updated Ave"
  }'

# Delete user
curl -X DELETE http://YOUR_EC2_IP:8080/api/users/1
```

## 🔧 Troubleshooting

### Common Issues:

1. **Connection Refused**
   - Check if MySQL is running: `sudo systemctl status mysql`
   - Verify security group allows port 3306
   - Test connection: `mysql -h YOUR_EC2_IP -u root -p`

2. **Access Denied**
   - Check MySQL user permissions
   - Ensure user can connect from remote hosts
   - Verify password is correct

3. **Port 8080 Not Accessible**
   - Check security group allows port 8080
   - Verify application is running: `ps aux | grep java`
   - Check logs: `tail -f app.log`

### Database Connection Test:
```bash
# Test MySQL connection from EC2
mysql -h localhost -u root -p -e "SHOW DATABASES;"

# Test from application
curl http://YOUR_EC2_IP:8080/api/hello
```

## ✅ Success Indicators

- ✅ Application starts without errors
- ✅ Database connection established
- ✅ Web interface loads at `http://YOUR_EC2_IP:8080`
- ✅ Can add/edit/delete users through web interface
- ✅ REST API responds correctly
- ✅ Data persists in MySQL database
- ✅ No errors in application logs

## 📊 Expected Database Schema

After first run, MySQL should have:
```sql
USE userdb;
DESCRIBE users;

-- Expected output:
-- +----------+--------------+------+-----+---------+----------------+
-- | Field    | Type         | Null | Key | Default | Extra          |
-- +----------+--------------+------+-----+---------+----------------+
-- | id       | bigint       | NO   | PRI | NULL    | auto_increment |
-- | name     | varchar(255) | NO   |     | NULL    |                |
-- | email    | varchar(255) | NO   | UNI | NULL    |                |
-- | phone    | varchar(255) | NO   |     | NULL    |                |
-- | address  | varchar(255) | YES  |     | NULL    |                |
-- +----------+--------------+------+-----+---------+----------------+
```