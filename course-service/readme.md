# Course Service

A Spring Boot microservice for managing courses, deployed on Google Cloud Platform with Cloud SQL integration.

## Prerequisites

- Java 21 or higher
- Maven 3.6+
- Google Cloud SDK (gcloud CLI)
- Access to Google Cloud Project: `gcp-convert-spring`

## Database Setup

### 1. Create the Database
Use Google Cloud Console to create the database:
1. Navigate to Cloud SQL → `mydbtesting` instance
2. Go to **Databases** tab
3. Click **Create Database**
4. Enter database name: `eca_courses`

### 2. Alternative: Using gcloud CLI
```bash
gcloud sql databases create eca_courses --instance=mydbtesting --project=gcp-convert-spring
```

## Local Development Setup

### 1. Authentication
Choose one of these methods:

**Option A: Application Default Credentials (Recommended)**
```bash
gcloud auth application-default login
gcloud config set project gcp-convert-spring
```

**Option B: Service Account Key**
1. Create a service account with `Cloud SQL Client` role
2. Download JSON key file
3. Set environment variable:
   ```bash
   export GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account-key.json
   ```

### 2. Environment Variables
Set the database password:
```bash
export DB_PASSWORD=your_database_password
```

## Configuration

### application.properties
```properties
spring.application.name=course-service
server.port=8081

# Google Cloud SQL Configuration
spring.cloud.gcp.sql.enabled=true
spring.cloud.gcp.sql.database-name=eca_courses
spring.cloud.gcp.sql.instance-connection-name=gcp-convert-spring:asia-southeast1:mydbtesting

# DataSource Configuration
spring.datasource.url=jdbc:mysql://google/eca_courses?cloudSqlInstance=gcp-convert-spring:asia-southeast1:mydbtesting&socketFactory=com.google.cloud.sql.mysql.SocketFactory&useSSL=false
spring.datasource.username=root
spring.datasource.password=${DB_PASSWORD}
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver

# JPA Configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MySQLDialect
spring.jpa.show-sql=true
```

## Running the Application

### 1. Build the project
```bash
mvn clean compile
```

### 2. Run the application
```bash
mvn spring-boot:run
```

### 3. Access the application
- Base URL: `http://localhost:8081`
- Health Check: `http://localhost:8081/actuator/health` (if actuator is enabled)

## Troubleshooting

### Common Issues

**Authentication Error**
```
Unable to obtain credentials to communicate with the Cloud SQL API
```
**Solution:** Ensure `gcloud auth application-default login` is run or `GOOGLE_APPLICATION_CREDENTIALS` is set.

**Database Not Found**
```
Unknown database 'eca_courses'
```
**Solution:** Create the database using Google Cloud Console or gcloud CLI.

**Connection Timeout**
- Verify Cloud SQL instance is running
- Check firewall rules
- Confirm instance connection name is correct

### Debugging Commands
```bash
# Check authentication
gcloud auth list

# Test Cloud SQL connection
gcloud sql connect mydbtesting --user=root --project=gcp-convert-spring

# List databases
gcloud sql databases list --instance=mydbtesting --project=gcp-convert-spring
```

## Project Structure

```
src/
├── main/
│   ├── java/
│   │   └── lk/ise/eca/course/
│   │       ├── CourseServiceApplication.java
│   │       ├── controller/
│   │       ├── entity/
│   │       ├── repository/
│   │       └── service/
│   └── resources/
│       └── application.properties
```

## Dependencies

Key dependencies include:
- Spring Boot 3.5.5
- Spring Data JPA
- Spring Cloud GCP SQL MySQL
- MySQL Connector
- Hibernate

## Security Notes

- Never commit database passwords or service account keys to version control
- Use environment variables for sensitive configuration
- Regularly rotate service account keys
- Follow principle of least privilege for IAM roles

## Deployment

For production deployment to Google Cloud, consider:
- Google Cloud Run
- Google Kubernetes Engine (GKE)
- Compute Engine with proper IAM service accounts

google drive link - https://drive.google.com/drive/folders/1owdEkjbkwxJux6bNBmlT_-Ngqp7p8IHk?usp=drive_link