# pricingui-tomcat test
FROM rhel7.2

# TODO: Put the maintainer name in the image metadata
MAINTAINER Mukul Nadkarni <mnadkarni@atpco.net>

RUN yum -y update; yum clean all
RUN yum -y install httpd; yum clean all
#RUN echo "Apache" >> /var/www/html/index.html

RUN rpm -Uvh https://dl.fedoraproject.org/pub/epel/7/x86_64/e/epel-release-7-8.noarch.rpm 
RUN yum install -y  --enablerepo rhel-7-server-optional-rpms --enablerepo rhel-7-server-rpms wget tar unzip bzip2 which  && \
    yum -y install git; yum clean all  && \
    git config --global url."https://".insteadOf git://   && \
    git config --global user.email "mukul37@gmail.com"   && \
    git config --global user.name "Mukul"

RUN yum -y install npm; yum clean all

RUN mkdir /opt/app-root

RUN npm install -g  bower grunt-cli tsd

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

RUN useradd -u 1001 testac
#RUN chown -R 1001:1001 /opt/openshift
#RUN chown -R 1001:1001 /usr/local/sti/* 

#RUN chown -R 1001:0 /usr/local/tomcat

#RUN chmod -R ug+rw /usr/local/tomcat
RUN chmod -R a+rwX /usr/local/sti/assemble
RUN chmod -R a+rwX /usr/local/sti/run
RUN chmod -R a+rwX /usr/local/sti/save-artifacts
RUN chmod -R a+rwX /usr/local/sti/usage

RUN chmod +x /usr/local/sti/assemble /usr/local/sti/run /usr/local/sti/save-artifacts /usr/local/sti/usage
RUN chmod +x /run/httpd
RUN chmod +x /run/httpd/*
RUN mkdir /var/www/html
RUN chmod +x /var/www/html/*
# Set the default port for applications built using this image
# TODO: Set the default port for applications built using this image
EXPOSE 8080

# TODO: Set the default CMD for the image
# CMD ["usage"]
# In order to drop the root user, we have to make some directories world
# writeable as OpenShift default security model is to run the container under
# random UID.
# RUN sed -i -f /opt/app-root/etc/httpdconf.sed /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf && \
# head -n151 /opt/rh/httpd24/root/etc/httpd/conf/httpd.conf | tail -n1 | grep "AllowOverride All" || exit && \
RUN sed -i \
    -e 's/^Listen 80/Listen 0.0.0.0:8080/' \
    -e 's/^User apache/User default/' \
    -e 's/^Group apache/Group root/' \
    -e 's%^DocumentRoot "/var/www/html"%DocumentRoot "/opt/app-root/src"%' \
    -e 's%^<Directory "/var/www/html"%<Directory "/opt/app-root/src"%' \
    -e 's%^<Directory "/var/html"%<Directory "/opt/app-root/src"%' \
    -e 's%^ErrorLog "logs/error_log"%ErrorLog "/tmp/error_log"%' \
    -e 's%CustomLog "logs/access_log"%CustomLog "|/usr/bin/cat"%' \
    -e '151s%AllowOverride None%AllowOverride All%' \
    /etc/httpd/conf/httpd.conf && \
    head -n151 /etc/httpd/conf/httpd.conf | tail -n1 | grep "AllowOverride All" || exit && \
    mkdir /tmp/sessions && \
    chmod -R a+rwx /var/run/httpd && \
    chmod -R a+rwx /tmp/sessions && \
    chown -R 1001:0  /opt/app-root /tmp/sessions


RUN cat /etc/sysconfig/httpd.conf 

USER 1001
# Set the default CMD for the image
# CMD ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/opt/openshift/app.jar"]
CMD ["usage"]
# Simple startup script to avoid some issues observed with container restart 
#ADD run-apache.sh /run-apache.sh
#RUN chmod -v +x /run-apache.sh

#CMD ["/run-apache.sh"]

# This default user is created in the openshift/base-centos7 image
#USER 1001
