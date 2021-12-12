FROM eclipse-temurin:17-alpine

# Non-root user for security purposes.
#
# A UID above 10,000 is less likely to 
# map to a more privileged user on the host
# in the case of a container breakout.
RUN addgroup --gid 10001 --system nonroot \
 && adduser --uid 10000 --system --ingroup nonroot \
 --no-create-home --disabled-password nonroot

# Initial and max heap size for the java application
ARG heap_size=4G
ENV HEAP_SIZE=$heap_size

# Java options
ARG java_tool_options="-Xms${HEAP_SIZE} -Xmx${HEAP_SIZE} -XX:+UseG1GC -XX:+ParallelRefProcEnabled -XX:MaxGCPauseMillis=200 -XX:+UnlockExperimentalVMOptions -XX:+DisableExplicitGC -XX:+AlwaysPreTouch -XX:G1NewSizePercent=30 -XX:G1MaxNewSizePercent=40 -XX:G1HeapRegionSize=8M -XX:G1ReservePercent=20 -XX:G1HeapWastePercent=5 -XX:G1MixedGCCountTarget=4 -XX:InitiatingHeapOccupancyPercent=15 -XX:G1MixedGCLiveThresholdPercent=90 -XX:G1RSetUpdatingPauseTimePercent=5 -XX:SurvivorRatio=32 -XX:+PerfDisableSharedMem -XX:MaxTenuringThreshold=1 -Dusing.aikars.flags=mcflags.emc.gs -Daikars.new.flags=true -Dcom.mojang.eula.agree=true"
ENV JAVA_TOOL_OPTIONS=$java_tool_options

# Tini spawns a child process and reaps zombie
# processes while waiting for the child to exit.
# See https://github.com/krallin/tini.
RUN apk add --no-cache tini
ENTRYPOINT ["/sbin/tini", "--", "java", "-jar", "/opt/purpur/purpur.jar"]

# URL for the Purpur jar
ARG purpur_version=1.18.1
ENV PURPUR_JAR_URL=https://api.purpurmc.org/v2/purpur/${purpur_version}/latest/download

# Add the Purpur jar
ADD --chown=nonroot:nonroot ${PURPUR_JAR_URL} /opt/purpur/purpur.jar

# Add data directory owned by the non-root user
RUN mkdir /data && chown -R nonroot:nonroot /data

# Add persistent storage for the Purpur data
VOLUME /data
# Change directory before running the application
WORKDIR /data

# Use the non-root user to run the application
USER nonroot

# Default arguments
CMD ["nogui"]
