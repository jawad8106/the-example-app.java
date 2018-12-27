FROM openjdk:8-jre-alpine

# Initializing variables that will be used later.
ENV APP_NAME=discovery_server
ENV SERVER_PORT=7001
ENV JAR_FILE=discovery-server-SNAPSHOT.jar


# Copy all the required files/directories
COPY ${APP_NAME}/*.jar /usr/src/${JAR_FILE}

# Startup Command
ENTRYPOINT java -Dserver.port=${SERVER_PORT} -Deureka.instance.preferIpAddress=true -jar /usr/src/${JAR_FILE}
