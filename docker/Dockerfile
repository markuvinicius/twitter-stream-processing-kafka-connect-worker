FROM confluentinc/cp-server-connect-base:7.3.0
USER root
ARG TWITTER_STREAM_PROCESSING_USER
ARG TWITTER_STREAM_PROCESSING_USER_ID
ARG JAVA_AGENT_PORT
ENV TWITTER_STREAM_PROCESSING_USER=${TWITTER_STREAM_PROCESSING_USER:-streamProcessingUser}
ENV JAVA_AGENT_PORT=${JAVA_AGENT_PORT:-8088}
ENV TWITTER_STREAM_PROCESSING_USER_ID=${TWITTER_STREAM_PROCESSING_USER_ID:-1010}

RUN groupadd --gid ${TWITTER_STREAM_PROCESSING_USER_ID} ${TWITTER_STREAM_PROCESSING_USER} ;\
    adduser --shell /bin/bash --gid ${TWITTER_STREAM_PROCESSING_USER_ID} --uid ${TWITTER_STREAM_PROCESSING_USER_ID} ${TWITTER_STREAM_PROCESSING_USER}

RUN usermod -a -G appuser ${TWITTER_STREAM_PROCESSING_USER}
RUN mkdir -p /app/lib
RUN mkdir -p /app/conf
WORKDIR /app
RUN chown ${TWITTER_STREAM_PROCESSING_USER}:${TWITTER_STREAM_PROCESSING_USER} /app
RUN chown -R ${TWITTER_STREAM_PROCESSING_USER}:${TWITTER_STREAM_PROCESSING_USER} /etc/kafka/
RUN chown -R ${TWITTER_STREAM_PROCESSING_USER}:${TWITTER_STREAM_PROCESSING_USER} /etc/kafka-connect/
RUN chown -R ${TWITTER_STREAM_PROCESSING_USER}:${TWITTER_STREAM_PROCESSING_USER} /usr/share/confluent-hub-components/
RUN chown -R ${TWITTER_STREAM_PROCESSING_USER}:${TWITTER_STREAM_PROCESSING_USER} /etc/schema-registry/
USER ${TWITTER_STREAM_PROCESSING_USER}

COPY target/jmx_prometheus_javaagent*.jar /app/lib/jmx_prometheus_javaagent.jar
COPY docker/jmx_exporter_config.yaml /app/conf/jmx_exporter_config.yaml

RUN confluent-hub install --no-prompt jcustenborder/kafka-connect-twitter:0.3.34

CMD export EXTRA_ARGS="-javaagent:/app/lib/jmx_prometheus_exporter-jar=${JAVA_AGENT_PORT}:/app/conf/jmx_exporter_config.yaml" ;\
    /etc/confluent/docker.run

