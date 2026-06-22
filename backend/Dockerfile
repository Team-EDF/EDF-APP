FROM gradle:8.7-jdk21 AS builder

WORKDIR /app

COPY EDF-BE/build.gradle EDF-BE/settings.gradle ./
COPY EDF-BE/gradle ./gradle
COPY EDF-BE/gradlew ./

RUN chmod +x gradlew

COPY EDF-BE/src ./src

RUN ./gradlew clean bootJar -x test


FROM eclipse-temurin:21-jre

WORKDIR /app

COPY --from=builder /app/build/libs/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]