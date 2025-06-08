# Base image with Java and Maven
FROM maven:3.8.8-eclipse-temurin-17

# Install Docker CLI and Git
RUN apt-get update && \
    apt-get install -y docker.io git && \
    apt-get clean

# Set workdir
WORKDIR /usr/src/app

CMD ["bash"]
