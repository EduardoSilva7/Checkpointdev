# Multi-stage Dockerfile: build with Maven, run on OpenJDK
FROM maven:3.8.8-openjdk-17-slim AS build
WORKDIR /build
COPY pom.xml .
COPY src ./src
RUN mvn -B -DskipTests package

FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=build /build/target/*.jar app.jar
RUN useradd -ms /bin/bash appuser || true
USER appuser
ENTRYPOINT ["java", "-jar", "app.jar"]
