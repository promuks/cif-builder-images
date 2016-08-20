# pricingui-tomcat test
FROM rhel7.2

# TODO: Put the maintainer name in the image metadata
MAINTAINER Mukul Nadkarni <mnadkarni@atpco.net>

# Install build tools on top of base image
# Java jdk 8, Maven 3.3
ENV MAVEN_VERSION 3.3.9
ENV TOMCAT_VERSION 8.0.36
ENV CATALINA_HOME /usr/local/tomcat
ENV JAVA_VERSION 8u45
ENV BUILD_VERSION b14

# Upgrading system
RUN yum install -y https://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-8.noarch.rpm && \
    yum update -y && \
    yum install -y  --enablerepo rhel-7-server-optional-rpms --enablerepo rhel-7-server-rpms wget tar unzip bzip2 bc which lsof && \
    yum clean all

# Install JDK
RUN wget --no-cookies --no-check-certificate \
        --header "Cookie: oraclelicense=accept-securebackup-cookie" \
        "http://download.oracle.com/otn-pub/java/jdk/$JAVA_VERSION-$BUILD_VERSION/jdk-$JAVA_VERSION-linux-x64.rpm" \
        -O /tmp/jdk-8-linux-x64.rpm && \
    yum install -y /tmp/jdk-8-linux-x64.rpm && \
    yum clean all && \
    rm -fr /tmp/jdk-8-linux-x64.rpm

# Install Apache Tomcat
RUN (curl -0 http://apache.spinellicreations.com/tomcat/tomcat-8/v"$TOMCAT_VERSION"/bin/apache-tomcat-"$TOMCAT_VERSION".tar.gz | \
    tar -zx -C /usr/local) && \
    mv /usr/local/apache-tomcat-"$TOMCAT_VERSION" "$CATALINA_HOME"

RUN mkdir -p /opt/openshift && \
    mkdir -p /opt/app-root/source && chmod -R a+rwX /opt/app-root/source && \
    mkdir -p /opt/s2i/destination && chmod -R a+rwX /opt/s2i/destination && \
    mkdir -p /opt/app-root/src && chmod -R a+rwX /opt/app-root/src

# Git configuration
RUN yum install -y git && \
    yum clean all && \
    git config --global url."https://".insteadOf git:// && \
    git config --global user.email "mukul37@gmail.com" && \
    git config --global user.name "Mukul"

# Install npm packages
RUN yum install -y npm && \
    yum clean all && \
    npm install -g bower grunt-cli tsd

#RUN bower install --allow-root

ENV PATH=/opt/maven/bin/:$PATH

ENV BUILDER_VERSION 1.0

LABEL io.k8s.description="Platform for building Angular applications with Npm , Bower and Grunt" \
      io.k8s.display-name="Tomcat builder 1.0" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,tomcat"

# TODO (optional): Copy the builder files into /opt/openshift
# COPY ./<builder_folder>/ /opt/openshift/
# COPY Additional files,configurations that we want to ship by default, like a default setting.xml

LABEL io.openshift.s2i.scripts-url=image:///usr/local/sti
#LABEL io.openshift.s2i.destination=image:///usr/local/src

COPY .sti/bin/ /usr/local/sti
COPY scripts/ /usr/local/tomcat/bin
COPY ldapcert/ /usr/local/tomcat/certs
RUN useradd -u 1001 testac && \
    chown -R 1001:1001 /opt/openshift && \
    chown -R 1001:0 /usr/local/tomcat
#RUN chown -R 1001:1001 /usr/local/sti/*

RUN chmod -R ug+rw /usr/local/tomcat && \
    chmod -R a+rwX /usr/local/sti/assemble \
                   /usr/local/sti/run \
                   /usr/local/sti/save-artifacts \
                   /usr/local/sti/usage && \
    chmod -R ug+rwx /usr/local/tomcat/bin/addldapcert.sh && \
    chmod +x /usr/local/sti/assemble \
             /usr/local/sti/run \
             /usr/local/sti/save-artifacts \
             /usr/local/sti/usage && \
    /usr/local/tomcat/bin/addldapcert.sh

# This default user is created in the openshift/base-centos7 image
USER 1001

# Set the default port for applications built using this image
EXPOSE 8080

# Set the default CMD for the image
# CMD ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/opt/openshift/app.jar"]
CMD ["usage"]
