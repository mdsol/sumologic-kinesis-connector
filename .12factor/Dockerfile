# --------------- Build Stage 1 ---------------

FROM mdsol/java8-jdk as builder
MAINTAINER "Tyler Rupppert" truppert@mdsol.com

RUN apt-get -yq update && \
    apt-get clean

# Add app directory
ADD . /home/app/worker
RUN chown -R app:app /home/app/worker

# Install Maven
WORKDIR /opt
RUN wget -O /opt/apache-maven-3.6.0-bin.tar.gz \
    http://mirrors.koehn.com/apache/maven/maven-3/3.6.0/binaries/apache-maven-3.6.0-bin.tar.gz && \
    tar -xvzf apache-maven-3.6.0-bin.tar.gz && \
    ln -s /opt/apache-maven-3.6.0/bin/mvn /usr/local/bin/mvn

USER app
WORKDIR /home/app/worker

# Build Uberjar
RUN mvn clean install

# --------------- Build Stage 2 ---------------

FROM mdsol/java8-jre as runtime

RUN apt-get -yq update && \
    apt-get clean

COPY --from=builder /home/app/worker/target/kinesis-sumologic-connector-0.2.jar /home/app/worker/target/kinesis-sumologic-connector-0.2.jar
RUN chown app:app /home/app/worker/target/kinesis-sumologic-connector-0.2.jar

USER app

CMD ["java", "-jar", "/home/app/worker/target/kinesis-sumologic-connector-0.2.jar", "use-env"]

