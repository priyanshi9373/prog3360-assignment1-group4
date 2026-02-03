# ---------- Build stage ----------
FROM eclipse-temurin:21 AS build
WORKDIR /workspace

# Copy build descriptors first (cache dependencies)
COPY pom.xml ./
COPY .mvn .mvn
COPY mvnw ./

RUN ./mvnw -q -DskipTests dependency:go-offline

# Copy source and build
COPY src ./src
RUN ./mvnw -q -DskipTests package

# ---------- Runtime stage ----------
FROM eclipse-temurin:21-jre
WORKDIR /app

# Install curl for Docker health checks
RUN apt-get update && apt-get install -y curl

# Run as non-root user
RUN useradd -ms /bin/bash spring
USER spring

# Copy JAR
COPY --from=build /workspace/target/*.jar /app/app.jar

EXPOSE 8080
ENTRYPOINT ["java","-jar","/app/app.jar"]
