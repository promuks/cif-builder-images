# springboot-sti
FROM rhel7.2
MAINTAINER Veerendra Akula <veerendra.aakula>

# Install build tools on top of base image
# Java jdk 8, Maven 3.3
ENV MAVEN_VERSION 3.3.9
ENV JAVA_VERSION 8u45
ENV BUILD_VERSION b14

RUN yum update -y && \
    yum install -y --enablerepo rhel-7-server-optional-rpms tar unzip bc which lsof && \
    yum clean all

# Install JDK
RUN yum install -y wget && \
    wget --no-cookies --no-check-certificate \
        --header "Cookie: oraclelicense=accept-securebackup-cookie" \
        "http://download.oracle.com/otn-pub/java/jdk/$JAVA_VERSION-$BUILD_VERSION/jdk-$JAVA_VERSION-linux-x64.rpm" \
        -O /tmp/jdk-8-linux-x64.rpm && \
    yum install -y /tmp/jdk-8-linux-x64.rpm && \
    yum remove -y wget && \
    yum clean all && \
    rm -fr /tmp/jdk-8-linux-x64.rpm

# Install Apache Maven
RUN (curl -0 http://apache.mirrors.lucidnetworks.net/maven/maven-3/"$MAVEN_VERSION"/binaries/apache-maven-"$MAVEN_VERSION"-bin.tar.gz | \
    tar -zx -C /usr/local) && \
    mv /usr/local/apache-maven-"$MAVEN_VERSION" /usr/local/maven && \
    ln -sf /usr/local/maven/bin/mvn /usr/local/bin/mvn

RUN mkdir -p /opt/openshift && \
    mkdir -p /opt/app-root/source && chmod -R a+rwX /opt/app-root/source && \
    mkdir -p /opt/s2i/destination && chmod -R a+rwX /opt/s2i/destination && \
    mkdir -p /opt/app-root/src && chmod -R a+rwX /opt/app-root/src

ENV PATH=/opt/maven/bin/:$PATH

ENV BUILDER_VERSION 1.0

LABEL io.k8s.description="Platform for building Spring Boot applications with maven" \
      io.k8s.display-name="Spring Boot builder 1.0" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,maven-3,springboot"

# TODO (optional): Copy the builder files into /opt/openshift
# COPY ./<builder_folder>/ /opt/openshift/
# COPY Additional files,configurations that we want to ship by default, like a default setting.xml

LABEL io.openshift.s2i.scripts-url=image:///usr/local/sti

COPY ./.sti/bin/ /usr/local/sti

RUN chown -R 1001:1001 /opt/openshift && \
    chmod -R a+rwX /usr/local/sti/assemble && \
    chmod -R a+rwX /usr/local/sti/run && \
    chmod -R a+rwX /usr/local/sti/save-artifacts && \
    chmod -R a+rwX /usr/local/sti/usage && \
    chmod +x /usr/local/sti/assemble /usr/local/sti/run /usr/local/sti/save-artifacts /usr/local/sti/usage


COPY ldapcert/ /usr/local/certs
COPY scripts/ /usr/local/certs
RUN chmod +x /usr/local/certs/addldapcert.sh && \
    /usr/local/certs/addldapcert.sh

# This default user is created in the openshift/base-centos7 image
USER 1001

# Set the default port for applications built using this image
EXPOSE 8080

# Set the default CMD for the image
# CMD ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/opt/openshift/app.jar"]
CMD ["usage"]
