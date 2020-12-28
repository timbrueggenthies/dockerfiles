ARG BASE_USER
ARG MAINTAINER
FROM arm32v7/debian:latest
MAINTAINER $MAINTAINER

# Install Packages (basic tools, cups, basic drivers, HP drivers)
RUN apt-get update
RUN apt-get install -y sudo
RUN apt-get install -y whois
RUN apt-get install -y cups
RUN apt-get install -y cups-client
RUN apt-get install -y cups-bsd
RUN apt-get install -y cups-filters
RUN apt-get install -y foomatic-db-compressed-ppds
RUN apt-get install -y printer-driver-all
RUN apt-get install -y openprinting-ppds
RUN apt-get install -y hpijs-ppds
RUN apt-get install -y hp-ppd
RUN apt-get install -y hplip
RUN apt-get install -y smbclient
RUN apt-get install -y printer-driver-cups-pdf

RUN apt-get clean
RUN rm -rf /var/lib/apt/lists/*

# Add user and disable sudo password checking
RUN useradd \
  --groups=sudo,lp,lpadmin \
  --create-home \
  --home-dir=/home/print \
  --shell=/bin/bash \
  --password=$(mkpasswd print) \
  print \
&& sed -i '/%sudo[[:space:]]/ s/ALL[[:space:]]*$/NOPASSWD:ALL/' /etc/sudoers

# Configure the service's to be reachable
RUN /usr/sbin/cupsd \
  && while [ ! -f /var/run/cups/cupsd.pid ]; do sleep 1; done \
  && cupsctl --remote-admin --remote-any --share-printers \
  && kill $(cat /var/run/cups/cupsd.pid)

# Patch the default configuration file to only enable encryption if requested
RUN sed -e '0,/^</s//DefaultEncryption IfRequested\n&/' -i /etc/cups/cupsd.conf

# Default shell
CMD ["/usr/sbin/cupsd", "-f"]
