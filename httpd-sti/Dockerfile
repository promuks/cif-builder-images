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


# This default user is created in the openshift/base-centos7 image
USER 1001

# Set the default port for applications built using this image
EXPOSE 80

# Set the default CMD for the image
# CMD ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/opt/openshift/app.jar"]
CMD ["usage"]
