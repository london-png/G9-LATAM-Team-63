# Etapa 1: Compilar con Maven (Java 21)
FROM maven:3.9-eclipse-temurin-21-alpine AS builder
WORKDIR /app
COPY pom.xml .
RUN mvn dependency:go-offline
COPY src ./src
RUN mvn clean package -DskipTests

# Etapa 2: Ejecutar con JRE ligero (Java 21)
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app

# AGREGA ESTO: instalar wget para el healthcheck
RUN apk add --no-cache wget

COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080

# HEALTHCHECK antes del ENTRYPOINT (más limpio)
#HEALTHCHECK --interval=30s --timeout=5s --start-period=60s --retries=3 \
  #CMD wget -qO- http://localhost:8080/actuator/health || exit 1

# Arregla la URL de PostgreSQL si Render la pasa sin "jdbc:" y luego inicia la app
CMD ["sh", "-c", "export SPRING_DATASOURCE_URL=$(echo $SPRING_DATASOURCE_URL | sed 's/^postgresql:/jdbc:postgresql:/'); java -jar -Dserver.port=${PORT:-8080} -Dspring.profiles.active=prod app.jar"]
  

ENTRYPOINT ["java", "-jar", "app.jar"]
