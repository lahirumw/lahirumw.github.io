---
layout: post
title: The 12-Factor App Methodology for Microservices
subtitle: 
cover-img: 
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Microservices, Scalability, Best Practices, Cloud Services, 12 Factor App]
author: keylearn
---

The 12-Factor App methodology was originally developed by engineers at Heroku as a set of best practices for building software-as-a-service (SaaS) applications.
Over time, it has been adopted by developers building microservices, as it emphasizes scalable, maintainable, and cloud-ready applications.
In this blog, we’ll explore the 12 factors with a focus on microservices architecture and provide practical examples along the way.

**1. Codebase: One Codebase, Multiple Deploys**

\\Principle: A microservice should have a single codebase that can be deployed to multiple environments.\\

You would deploy this same codebase to multiple environments (e.g., dev, staging, prod), while environment-specific configurations would be kept external.

~~~
# Deploying to staging
mvn clean install
java -jar target/user-service.jar --spring.profiles.active=staging

# Deploying to production
mvn clean install
java -jar target/user-service.jar --spring.profiles.active=prod
~~~

**2. Dependencies: Explicitly Declare Dependencies**

\\Principle: Use a dependency management tool to explicitly declare all dependencies.\\

Example (Maven): In Spring Boot, dependencies are managed through pom.xml (Maven) or build.gradle (Gradle). All dependencies should be declared, and
the project should be able to be built by anyone with the right environment.

~~~
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
</dependencies>
~~~

This ensures that running mvn clean install will download all the required dependencies.

**3. Config: Store Configuration in the Environment**

\\Principle: Configuration should be stored in the environment, not in the code.\\

Example: In Spring Boot, environment-specific configurations are stored in external property files (application.properties or application.yml), or
injected via environment variables.

~~~
# application.properties
spring.datasource.url=${DATABASE_URL}
spring.datasource.username=${DB_USER}
spring.datasource.password=${DB_PASSWORD}
jwt.secret=${JWT_SECRET}
~~~

During runtime, you can pass the values using environment variables or system properties:

~~~
# Pass environment variables at runtime
DATABASE_URL=jdbc:postgresql://localhost:5432/mydb \
DB_USER=admin \
DB_PASSWORD=password \
JWT_SECRET=mysecret \
java -jar target/user-service.jar
~~~

**4. Backing Services: Treat Backing Services as Attached Resources**

\\Principle: Treat backing services (like databases, queues) as attached resources.\\

Example: A Spring Boot microservice connecting to PostgreSQL can be configured to connect to a local database in development and an external
managed database in production by simply changing the connection string:

~~~
# application-dev.properties (for local development)
spring.datasource.url=jdbc:postgresql://localhost:5432/devdb

# application-prod.properties (for production)
spring.datasource.url=jdbc:postgresql://rds.amazonaws.com:5432/proddb
~~~

**5. Build, Release, Run: Strictly Separate Build and Run Stages**

\\Principle: Separate the build, release, and run stages.\\

Example: With Spring Boot, you can define separate Maven build profiles and Docker configurations for different environments.

Build stage: Use Maven to build the artifact:

~~~
mvn clean package
~~~

Release stage: Create a Docker image:

~~~
docker build -t user-service:1.0 .
~~~

Run stage: Deploy the Docker image with the environment configuration:

~~~
docker run -e SPRING_PROFILES_ACTIVE=prod user-service:1.0
~~~

The service logic remains the same, and only the resource (database URL) changes.

**6. Processes: Execute the App as One or More Stateless Processes**

\\Principle: Microservices should be stateless, with state stored externally.\\

Example: In Spring Boot, keep services stateless by storing any session data in external systems like Redis or databases. Use annotations
like @Transactional to manage database transactions but avoid holding state in the service itself.

~~~
@RestController
public class PaymentController {

    @PostMapping("/payment")
    public ResponseEntity<String> processPayment(@RequestBody PaymentRequest paymentRequest) {
        // Stateless service logic
        paymentService.process(paymentRequest);
        return ResponseEntity.ok("Payment processed");
    }
}
~~~

To make this stateless, ensure any state (such as user session) is stored in an external Redis or database.

**7. Port Binding: Export Services via Port Binding**

\\Principle: The service should bind to a specific port and run independently.\\

Example: In Spring Boot, the service binds to a port via configuration in application.properties or through environment variables:

~~~
# application.properties
server.port=8080
~~~

Alternatively, use environment variables:

~~~
java -jar user-service.jar - server.port=9090
~~~

This allows your service to bind to any available port and communicate via HTTP.

**8. Concurrency: Scale Out via the Process Model**

\\Principle: Scale microservices by running multiple instances.\\

Example: You can scale a Spring Boot microservice by deploying multiple instances in Docker or Kubernetes.

~~~
# Kubernetes Deployment to scale microservice replicas
apiVersion: apps/v1
kind: Deployment
metadata:
 name: user-service
spec:
 replicas: 3
 template:
 spec:
 containers:
 - name: user-service
 image: user-service:latest
 ports:
 - containerPort: 8080
~~~

This deployment creates three replicas of the user-service, all stateless and capable of handling requests independently.

**9. Disposability: Maximize Robustness with Fast Startup and Graceful Shutdown**

\\Principle: The service should start quickly and shut down gracefully.\\

Example: In Spring Boot, you can ensure graceful shutdown by listening for termination signals like SIGTERM:

~~~
@PreDestroy
public void onExit() {
 System.out.println("Service is shutting down…");
}
~~~

This ensures that the microservice can complete any in-progress requests before shutting down, making it robust and fault-tolerant.

**10. Dev/Prod Parity: Keep Development, Staging, and Production as Similar as Possible**

\\Principle: Ensure parity between development, staging, and production environments.\\

Example: Use Docker to maintain consistency between environments. You can build the same Docker image for all environments and configure them using environment-specific settings.

~~~
# In development
docker build -t user-service:dev .
# In production
docker build -t user-service:prod .
~~~

This ensures consistency between how the app runs locally and in production.

**11. Logs: Treat Logs as Event Streams**

\\Principle: Microservices should stream logs to stdout or stderr.\\

Example: In Spring Boot, you can configure logs to be output to the console, and use tools like ELK or AWS CloudWatch to aggregate and analyze logs.

~~~
# application.properties
logging.level.org.springframework=INFO
logging.file=STDOUT
~~~

Then, a log aggregator tool (such as ELK) can capture these logs for later analysis.

**12. Admin Processes: Run Admin/Management Tasks as One-off Processes**

\\Principle: Admin or maintenance tasks should be run as one-off processes.\\

Example: For database migrations, use tools like Flyway or Liquibase to run migrations as separate tasks:

~~~
# Run Flyway migrations as one-off tasks
mvn flyway:migrate
~~~

This ensures that your microservice only handles its core responsibilities, while admin tasks are handled independently.

**Why 12 factor beneficial:**

1. Scalability
The 12-Factor App encourages statelessness and process-based scaling (Factor 6: Processes and Factor 8: Concurrency).
This makes scaling your microservices horizontally (i.e., by adding more instances) much easier, which is critical for handling varying loads in a cloud environment.
Example: If your application sees a sudden spike in traffic, you can easily scale out by adding more instances of your microservice without worrying about state synchronization.

2. Portability
Following 12-Factor principles, especially storing configuration in the environment (Factor 3: Config) and treating backing
services as attached resources (Factor 4: Backing Services), ensures that your microservices are portable across environments, cloud providers, and deployment setups.
Example: You can move your microservice from AWS to Google Cloud or from Kubernetes to another orchestration platform with minimal changes, as the configuration is externalized.

3. Faster Development and Deployment
By clearly separating code from configuration and using a well-defined build, release, and run cycle (Factor 5: Build, Release, Run), you can create a CI/CD pipeline that automates builds and deployments.
Example: When a developer pushes new code to a branch, it can automatically go through the build process, and once tested, can be released and deployed without manual intervention.

4. Improved Developer Productivity
The guidelines emphasize keeping development, staging, and production environments as similar as possible (Factor 10: Dev/Prod Parity). This consistency helps prevent the infamous “it works on my machine” issue.
Example: Developers can use Docker to replicate the production environment locally, reducing the chance of bugs slipping through because of environment-specific issues.

5. Maintainability
Clear separation of concerns (like keeping admin tasks separate as one-off processes in Factor 12: Admin Processes) and externalizing configuration makes microservices easier to maintain over time. It’s easier to troubleshoot, update, and monitor.
Example: You can run database migrations, backups, and other admin tasks separately from the main service without impacting its uptime or performance.

6. Resilience and Reliability
The 12-Factor App methodology promotes disposability (Factor 9: Disposability), meaning your microservices should start quickly, scale easily, and shut down gracefully. This improves system resilience, as microservices can be replaced without causing downtime.
Example: If a microservice crashes, orchestrators like Kubernetes can spin up new instances without data loss or interruption of service.

7. Cloud-Native Compatibility
The 12-Factor principles are aligned with cloud-native best practices, making it easier to deploy microservices on platforms like AWS, Google Cloud, or Azure. They focus on leveraging environment variables, statelessness, and treating everything as a service, which is ideal for cloud-based deployment.
Example: If you’re running on a platform like Heroku, Google Kubernetes Engine (GKE), or AWS Elastic Beanstalk, the 12-Factor principles make it easier to deploy, monitor, and manage your services in a cloud-native way.

8. Logging and Monitoring
Treating logs as event streams (Factor 11: Logs) allows you to centralize logs from all your microservices, which is crucial for observability in large, distributed systems.
Example: In a production system, using a log aggregator like ELK or AWS CloudWatch helps you monitor logs centrally, making debugging and analytics easier.

9. Ease of Onboarding New Developers
When projects follow consistent and well-documented patterns like the 12-Factor App methodology, new developers can more easily understand how the system works and get productive faster.
Example: With all dependencies clearly defined (Factor 2: Dependencies), a new developer can clone the repository, install dependencies with one command (like mvn install or gradle build), and run the service without needing to configure anything manually.

10. Support for CI/CD
The 12-Factor methodology, by enforcing a clear distinction between build, release, and run phases, aligns perfectly with continuous integration and continuous delivery (CI/CD) pipelines.
Example: Tools like Jenkins, GitLab CI, or GitHub Actions can build your project, run tests, and release the application automatically, ensuring high code quality and frequent, reliable deployments.

**When Not to Follow 12-Factor Strictly**

While following the 12-Factor App methodology is generally a best practice, there are scenarios where it might not be strictly necessary or ideal:

1. Small, Monolithic Applications: If you’re working on a small application or monolith that doesn’t require high scalability or cloud deployment, the 12-Factor principles might be overkill. For example, a simple app might not need full environment-based configuration management.

2. Stateful Applications: If your application requires heavy state management (e.g., financial systems, gaming), you might need to break the stateless rule (Factor 6: Processes) and incorporate some form of stateful management, although you can often offload state to external systems like databases or distributed caches.

3. Real-Time Systems: Some real-time systems that require very low latency and fast response times may not strictly adhere to certain 12-Factor principles, especially around disposability and port binding.

**Conclusion**

The 12-Factor App methodology provides a solid, well-structured approach to developing microservices that are cloud-native, scalable, and easy to maintain. While it’s not necessary to follow every factor for all projects, adopting these principles can significantly improve the scalability, resilience, and maintainability of your microservices, especially in large-scale, distributed, or cloud-based systems.

Reference : https://12factor.net/

