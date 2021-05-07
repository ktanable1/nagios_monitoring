# Dockerfile
FROM centos:7

# Update
RUN echo "nameserver 192.168.1.1" > /etc/resolv.conf
RUN yum clean all
RUN yum update -y

# Install prerequisites
RUN yum install -y epel-release gcc glibc glibc-common which wget unzip httpd php gd gd-devel perl postfix make openssl openssl-devel vim

# Set the working directory
WORKDIR /nagios/

# Copy into /nagios
COPY . /nagios/

# Download source
RUN wget -O nagioscore.tar.gz https://github.com/NagiosEnterprises/nagioscore/archive/nagios-4.4.6.tar.gz
RUN tar xzf nagioscore.tar.gz

# Set the working directory
WORKDIR /nagios/nagioscore-nagios-4.4.6/

# Compile
RUN ./configure
RUN make all

# Create User and Group
RUN make install-groups-users
RUN usermod -a -G nagios apache

# Install Binaries
RUN make install

# Install Service-Daemon
RUN make install-daemoninit
RUN systemctl enable httpd.service

# Install Command Mode
RUN make install-commandmode

# Install Configuration Files
RUN make install-config

# Install Apache Config Files
RUN make install-webconf

# Create nagiosadmin User Account
RUN htpasswd -b -c /usr/local/nagios/etc/htpasswd.users nagiosadmin nagiosadminnagiosadminnagiosadminnagiosadmin

# Install Nagios plugins dependencies
RUN yum install -y which gettext automake autoconf openssl-devel net-snmp net-snmp-utils
RUN yum install -y perl-Net-SNMP

# Set working directory
WORKDIR /nagios/

# Download Nagios Plugins
RUN wget --no-check-certificate -O /nagios/nagios-plugins.tar.gz https://nagios-plugins.org/download/nagios-plugins-2.3.3.tar.gz
RUN tar zxf nagios-plugins.tar.gz

# Set working directory
WORKDIR /nagios/nagios-plugins-2.3.3/

# Install Nagios plugins
RUN ./configure
RUN make
RUN make install

# Set working directory
WORKDIR /nagios/

# Download NRPE
RUN wget --no-check-certificate -O /nagios/nrpe.tar.gz  https://github.com/NagiosEnterprises/nrpe/releases/download/nrpe-4.0.3/nrpe-4.0.3.tar.gz
RUN tar xzf nrpe.tar.gz  

# Set working directory
WORKDIR /nagios/nrpe-4.0.3/

# Install NRPE
RUN ./configure
RUN make check_nrpe
RUN make install-plugin 

# Set working directory
WORKDIR /nagios/

# redirect
COPY index.html /var/www/html/

# Start Apache and Nagios
CMD ["/bin/bash", "/nagios/start.sh"]

