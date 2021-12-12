FROM eclipse-temurin:11-alpine

# Non-root user for security purposes.
#
# A UID above 10,000 is less likely to 
# map to a more privileged user on the host
# in the case of a container breakout.
RUN addgroup --gid 10001 --system nonroot \
 && adduser --uid 10000 --system --ingroup nonroot --home /home/nonroot nonroot

# Initial and max heap size for the java application
ARG heap_size=1G
ENV HEAP_SIZE=$heap_size

# Java options
ARG java_tool_options="-Xms${HEAP_SIZE} -Xmx${HEAP_SIZE} -XX:+UseG1GC -XX:G1HeapRegionSize=4M -XX:+UnlockExperimentalVMOptions -XX:+ParallelRefProcEnabled -XX:+AlwaysPreTouch -XX:MaxInlineLevel=15"
ENV JAVA_TOOL_OPTIONS=$java_tool_options

# Tini spawns a child process and reaps zombie
# processes while waiting for the child to exit.
# See https://github.com/krallin/tini.
RUN apk add --no-cache tini
ENTRYPOINT ["/sbin/tini", "--", "java", "-jar", "/opt/velocity/velocity.jar"]

# URL for the Velocity jar
ARG velocity_jar_url=https://papermc.io/api/v2/projects/velocity/versions/3.1.1/builds/98/downloads/velocity-3.1.1-98.jar
ENV VELOCITY_JAR_URL=$velocity_jar_url

# Add the Velocity jar
ADD --chown=nonroot:nonroot ${VELOCITY_JAR_URL} /opt/velocity/velocity.jar

# Add persistent storage for the velocity configuration
VOLUME /home/nonroot
# Change directory before running the application
WORKDIR /home/nonroot

# Use the non-root user to run the application
USER nonroot
