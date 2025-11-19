# Base image: Maven with Java 21 (Temurin)
FROM maven:3.9.6-eclipse-temurin-21

# Install Docker CLI and git (git is usually required in Jenkins pipelines)
USER root
RUN apt-get update && \
    apt-get install -y docker.io git && \
    rm -rf /var/lib/apt/lists/*

# Default workdir
WORKDIR /workspace

# Print versions for debugging
RUN java -version && mvn -v && docker --version && git --version

# Keep the container alive for Jenkins agent
CMD ["tail", "-f", "/dev/null"]
