# springboot-sti
FROM rhel7.2
MAINTAINER Veerendra Akula <veerendra.aakula>

# Install build tools on top of base image
# Java jdk 8, Maven 3.3
ENV MAVEN_VERSION 3.3.9

RUN yum install -y --enablerepo rhel-7-server-optional-rpms tar unzip bc which lsof

ENV JAVA_VERSION 8u45
ENV BUILD_VERSION b14

# Upgrading system
RUN yum -y upgrade
RUN yum -y install wget

# Downloading Java
RUN wget --no-cookies --no-check-certificate --header "Cookie: oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/$JAVA_VERSION-$BUILD_VERSION/jdk-$JAVA_VERSION-linux-x64.rpm" -O /tmp/jdk-8-linux-x64.rpm

RUN yum -y install /tmp/jdk-8-linux-x64.rpm
    
#tar unzip bc which lsof java-1.8.0-openjdk java-1.8.0-openjdk-devel && \

RUN yum clean all -y && \
    (curl -0 http://apache.mirrors.lucidnetworks.net/maven/maven-3/3.3.9/binaries/apache-maven-3.3.9-bin.tar.gz | \
    tar -zx -C /usr/local) && \
    mv /usr/local/apache-maven-$MAVEN_VERSION /usr/local/maven && \
    ln -sf /usr/local/maven/bin/mvn /usr/local/bin/mvn && \
    mkdir -p /opt/openshift && \
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

RUN chown -R 1001:1001 /opt/openshift

RUN chmod -R a+rwX /usr/local/sti/assemble
RUN chmod -R a+rwX /usr/local/sti/run
RUN chmod -R a+rwX /usr/local/sti/save-artifacts
RUN chmod -R a+rwX /usr/local/sti/usage

RUN chmod +x /usr/local/sti/assemble /usr/local/sti/run /usr/local/sti/save-artifacts /usr/local/sti/usage


ADD ldapcert /usr/local/certs
ADD scripts/ /usr/local/certs
RUN chmod -R ug+rwx /usr/local/certs/addldapcert.sh
RUN chmod +x /usr/local/certs/addldapcert.sh
RUN /usr/local/certs/addldapcert.sh

# This default user is created in the openshift/base-centos7 image
USER 1001

# Set the default port for applications built using this image
EXPOSE 8080

# Set the default CMD for the image
# CMD ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/opt/openshift/app.jar"]
CMD ["usage"]
