FROM jboss/keycloak:latest

USER root
RUN yum install -y mc && yum install -y tcpdump && yum install -y bind-utils && yum install -y iproute && yum install -y nmap && yum install -y sudo
RUN echo "jboss" | passwd --stdin jboss
RUN usermod -aG wheel jboss
USER jboss
