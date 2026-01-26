# Stage 1: Runtime environment
FROM eclipse-temurin:21-jdk-jammy

# Set the working directory inside the container
WORKDIR /app

# Copy the JAR file produced by the Maven build in GitHub Actions
# Note: Ensure your pom.xml generates a JAR that matches this pattern
COPY target/*.jar app.jar

# Expose the default Spring Boot port
EXPOSE 8080

# Execute the application
ENTRYPOINT ["java", "-jar", "app.jar"]
