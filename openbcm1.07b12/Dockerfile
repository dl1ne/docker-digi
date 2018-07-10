# Set the base image
FROM debian

# Arguments
ARG boxaddr="MYCALL.#BAY.DEU.EU"
ARG mycall="N0CALL"
ARG sysop="N0CALL"
ARG guest="GUEST"
ARG http="8080"
ARG ftp="8021"
ARG smtp="8025"
ARG pop3="8110"
ARG nntp="8119"
ARG telnet="4719"
ARG httpaccount="1"
ARG httpguestfirst="1"
ARG disable="0"
ARG defhttpsurface="1"
ARG my_port="10091"
ARG tnn_host="tnn"
ARG tnn_port="10093"

# Environment
ENV SHELL=/bin/bash

# Dockerfile author / maintainer
MAINTAINER Simon Brecht <simon@brecht.email>

# Add Scripts to Image
ADD . /scripts

# Workaround for Windows Newline
RUN sed -i -e 's/\r$//' /scripts/start_bcm \
 && sed -i -e 's/\r$//' /scripts/wizard_1st \
 && sed -i -e 's/\r$//' /scripts/wizard_2nd \
 && sed -i -e 's/\r$//' /scripts/wizard_g++-4.9-libs

# Update application repository list and install common Linux tools
#RUN apt-get update && apt-get install -y git wget curl \
# && apt-get install ax25* libax25* make gcc bzip2 screen g++ -y
RUN apt-get update && apt-get install -y git wget curl \
 && apt-get install make bzip2 -y

# =================================================================================
# START: Install OpenBCM
#
WORKDIR /usr/local/src
RUN git clone https://github.com/oe5hpm/openBCM.git
WORKDIR /usr/local/src/openBCM
#
# patch makefile
RUN patch makefile /scripts/makefile.patch
#
# install g++-4.9
RUN /scripts/wizard_g++-4.9-libs
#
# run make
RUN make
#
# deploy manual, makefile doesnt work
RUN mkdir /usr/local/openbcm \
 && cp bcm /usr/local/openbcm/
#
# lets create default files for openbcm
WORKDIR /usr/local/openbcm
RUN /scripts/wizard_1st
#
# set config from build args
RUN sed -i "s/^boxaddress.*/boxaddress ${boxaddr}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^sysopcall.*/sysopcall ${sysop}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^guestcall.*/guestcall ${guest}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^ftp_port.*/ftp_port ${ftp}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^smtp_port.*/smtp_port ${smtp}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^pop3_port.*/pop3_port ${pop3}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^nntp_port.*/nntp_port ${nntp}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^telnet_port.*/telnet_port ${telnet}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^radio_port.*/radio_port 1/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^httpaccount.*/httpaccount ${httpaccount}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^httpguestfirst.*/httpguestfirst ${httpguestfirst}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^disable.*/disable ${disable}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^defhttpsurface.*/defhttpsurface ${defhttpsurface}/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^internetmailgate.*/internetmailgate ON/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^mycall.*/mycall ${mycall}-4/g" /usr/local/openbcm/init.bcm \
 && sed -i "s/^dcall.*/dcall ${mycall}-4/g" /usr/local/openbcm/init.l2 \
 && sed -i "s/^peer.*/peer ${tnn_host}/g" /usr/local/openbcm/init.l2 \
 && sed -i "s/^port.*/port ${my_port}/g" /usr/local/openbcm/init.l2 \
 && sed -i "s/^txport.*/txport ${tnn_port}/g" /usr/local/openbcm/init.l2
#
# END: Install OpenBCM
# =================================================================================

ENTRYPOINT ["/scripts/start_bcm"]
