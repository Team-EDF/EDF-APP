# =========================
# 1단계: Build Stage 
# =========================
FROM gradle:8.7-jdk17 AS builder

WORKDIR /app

COPY build.gradle settings.gradle ./
COPY gradle ./gradle
COPY gradlew ./

RUN chmod +x gradlew

COPY src ./src

RUN ./gradlew clean bootJar -x test


# =========================
# 2단계: Runtime Stage
# =========================
FROM eclipse-temurin:17-jre

WORKDIR /app

COPY --from=builder /app/build/libs/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]