FROM maven:3.8.7-eclipse-temurin-17 AS build

WORKDIR /workspace
COPY spring-boot-backend-apirest/ ./spring-boot-backend-apirest/
WORKDIR /workspace/spring-boot-backend-apirest

COPY ../ojdbc8.jar /tmp/ojdbc8.jar

RUN if [ -f /tmp/ojdbc8.jar ]; then \
			echo "Found ojdbc8.jar in build context - installing into local maven repo"; \
			mvn install:install-file -Dfile=/tmp/ojdbc8.jar -DgroupId=com.oracle -DartifactId=ojdbc8 -Dversion=12.2.0.1 -Dpackaging=jar; \
		else \
			echo "No ojdbc8.jar found in build context. If your project requires Oracle JDBC driver, add Server/ojdbc8.jar or configure an alternate repository."; \
		fi

RUN mvn -B -DskipTests package --no-transfer-progress


FROM eclipse-temurin:17-jre

RUN addgroup --system appgroup || true && adduser --system --ingroup appgroup appuser || true

WORKDIR /app

COPY --from=build /workspace/spring-boot-backend-apirest/target/*.jar /app/app.jar

EXPOSE 8082

USER appuser

ENV JAVA_OPTS="-Xms256m -Xmx512m"

ENTRYPOINT ["sh", "-c", "exec java $JAVA_OPTS -jar /app/app.jar"]
