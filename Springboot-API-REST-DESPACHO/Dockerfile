 # ── STAGE 1: Build ──────────────────────────────
FROM maven:3.9-eclipse-temurin-17-alpine AS builder

WORKDIR /app

# Copiamos primero el pom.xml para cachear dependencias
COPY pom.xml .
RUN mvn dependency:go-offline -B

# Copiamos el código fuente y construimos
COPY src ./src
RUN mvn clean package -DskipTests

# ── STAGE 2: Run ─────────────────────────────────
FROM eclipse-temurin:17-jre-alpine AS production

WORKDIR /app

# Creamos usuario no root (seguridad)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copiamos el jar generado
COPY --from=builder /app/target/*.jar app.jar

# Permisos al usuario no root
RUN chown -R appuser:appgroup /app
USER appuser

EXPOSE 8080

CMD ["java", "-jar", "app.jar"]
