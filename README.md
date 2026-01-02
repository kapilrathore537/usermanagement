# User Management System

A complete Spring Boot application with MySQL database integration for managing users. Features a web interface for CRUD operations and REST API endpoints.

## Features

- ✅ Add new users with form validation
- ✅ View all users in a responsive table
- ✅ Edit existing user information
- ✅ Delete users with confirmation
- ✅ REST API endpoints for all operations
- ✅ MySQL database integration
- ✅ Bootstrap UI with responsive design
- ✅ AWS EC2 deployment ready

## Technology Stack

- **Backend**: Spring Boot 3.5.9, Spring Data JPA
- **Frontend**: Thymeleaf, Bootstrap 5, Font Awesome
- **Database**: MySQL 8.0+
- **Build Tool**: Maven

## Quick Start (Local with Docker)

1. **Start the application with Docker Compose:**
   ```bash
   docker-compose up -d
   ```

2. **Access the application:**
   - Web Interface: http://localhost:8080
   - REST API: http://localhost:8080/api/users

## AWS EC2 Deployment

### Prerequisites
- AWS EC2 instance with Java 17+ installed
- MySQL server running on EC2 or RDS
- Security groups configured for ports 8080 and 3306

### Steps

1. **Update database configuration:**
   Edit `src/main/resources/application-aws.yaml`:
   ```yaml
   spring:
     datasource:
       url: jdbc:mysql://YOUR_EC2_IP:3306/userdb
       username: your_mysql_user
       password: your_mysql_password
   ```

2. **Create MySQL database:**
   ```sql
   CREATE DATABASE userdb;
   CREATE USER 'appuser'@'%' IDENTIFIED BY 'your_password';
   GRANT ALL PRIVILEGES ON userdb.* TO 'appuser'@'%';
   FLUSH PRIVILEGES;
   ```

3. **Build and deploy:**
   ```bash
   # Build the application
   mvn clean package -DskipTests
   
   # Copy to EC2 instance
   scp target/demo-0.0.1-SNAPSHOT.jar ec2-user@YOUR_EC2_IP:~/
   
   # Run on EC2
   ssh ec2-user@YOUR_EC2_IP
   java -jar demo-0.0.1-SNAPSHOT.jar --spring.profiles.active=aws
   ```

## API Endpoints

### REST API
- `GET /api/users` - Get all users
- `GET /api/users/{id}` - Get user by ID
- `POST /api/users` - Create new user
- `PUT /api/users/{id}` - Update user
- `DELETE /api/users/{id}` - Delete user

### Web Interface
- `GET /` - User list page
- `GET /add-user` - Add user form
- `POST /add-user` - Submit new user
- `GET /edit-user/{id}` - Edit user form
- `POST /edit-user/{id}` - Update user
- `GET /delete-user/{id}` - Delete user

## Testing the Connection

1. **Check database connection:**
   ```bash
   curl http://localhost:8080/api/hello
   ```

2. **Test user creation:**
   ```bash
   curl -X POST http://localhost:8080/api/users \
     -H "Content-Type: application/json" \
     -d '{
       "name": "John Doe",
       "email": "john@example.com",
       "phone": "1234567890",
       "address": "123 Main St"
     }'
   ```

3. **Get all users:**
   ```bash
   curl http://localhost:8080/api/users
   ```

## Database Schema

The application automatically creates the following table:

```sql
CREATE TABLE users (
    id BIGINT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) NOT NULL UNIQUE,
    phone VARCHAR(255) NOT NULL,
    address VARCHAR(255)
);
```

## Configuration Files

- `application.yaml` - Default configuration (Docker)
- `application-aws.yaml` - AWS EC2 specific configuration
- `docker-compose.yml` - Local development with Docker

## Troubleshooting

### Common Issues

1. **Connection refused:**
   - Check if MySQL is running
   - Verify security group settings
   - Ensure correct IP/hostname in configuration

2. **Access denied:**
   - Verify MySQL user credentials
   - Check user permissions
   - Ensure MySQL allows remote connections

3. **Port already in use:**
   - Change server port in application.yaml
   - Kill existing processes: `sudo lsof -t -i:8080 | xargs kill -9`

### Logs
Check application logs for detailed error information:
```bash
tail -f logs/spring.log
```