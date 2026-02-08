# Copyright The OpenTelemetry Authors
# SPDX-License-Identifier: Apache-2.0


FROM eclipse-temurin:21-jdk@sha256:c57968360245ecac4228c5a9cfb56d4a02f0a67143c7e4dbf9f4ef5beb5253ec as builder

WORKDIR /usr/src/app/

COPY ./src/adservice/gradlew* ./src/adservice/settings.gradle* ./src/adservice/build.gradle ./
COPY ./src/adservice/gradle ./gradle

RUN ./gradlew
RUN ./gradlew downloadRepos

COPY ./src/adservice/ ./
COPY ./pb/ ./proto
RUN ./gradlew installDist -PprotoSourceDir=./proto

# -----------------------------------------------------------------------------

FROM eclipse-temurin:21-jre@sha256:ff7e4a51821d2ceee20fa05064da366f3c12be8ed18596dd2ad0999b2af3fea0

ARG version=2.6.0
WORKDIR /usr/src/app/

COPY --from=builder /usr/src/app/ ./
ADD --chmod=644 https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/download/v$version/opentelemetry-javaagent.jar /usr/src/app/opentelemetry-javaagent.jar
ENV JAVA_TOOL_OPTIONS=-javaagent:/usr/src/app/opentelemetry-javaagent.jar

EXPOSE ${AD_SERVICE_PORT}
ENTRYPOINT [ "./build/install/opentelemetry-demo-ad-service/bin/AdService" ]
