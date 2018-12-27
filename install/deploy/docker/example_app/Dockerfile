FROM openjdk:8-jre-alpine

# Initializing variables that will be used later.
ENV APP_NAME=example_app
#ENV SERVER_PORT=7001
#ENV JAR_FILE=discovery-server-SNAPSHOT.jar

# create directory
RUN mkdir -p /usr/src/${APP_NAME}

# Copy all the required files/directories
COPY ${APP_NAME}.tar.gz /usr/src/${APP_NAME}/

WORKDIR /usr/src/${APP_NAME}

# Startup Command
#ENTRYPOINT ./gradlew run
