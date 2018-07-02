# Set the base image
FROM debian

# Arguments
ARG pwsysop
ARG pwconsole
ARG nodeident
ARG nodecall
ARG port1=""
ARG port2=""
ARG port3=""
ARG port4=""
ARG port5=""
ARG port6=""
ARG port7=""
ARG port8=""
ARG port9=""
ARG port10=""
ARG port11=""
ARG port12=""
ARG port13=""
ARG port14=""
ARG port15=""

# Environment
ENV SHELL=/bin/bash

# Dockerfile author / maintainer
MAINTAINER Simon Brecht <simon@brecht.email>

# Add Scripts to Image
ADD . /scripts

# Update application repository list and install common Linux tools
#RUN apt-get update && apt-get install -y git wget curl \
# && apt-get install ax25* libax25* make gcc bzip2 screen -y
RUN apt-get update && apt-get install -y git wget curl \
 && apt-get install make gcc bzip2 screen -y

# =================================================================================
# START: Install TNN
#
RUN mkdir /usr/local/src/tnn
WORKDIR /usr/local/src/tnn
RUN wget --user-agent="Mozilla/5.0 (Macintosh; Intel Mac OS X 10.9; rv:32.0) Gecko/20100101 Firefox/32.0" https://dl6mpg.de/nordlink/ftp/pub/thenetnode/i386/1.79/179mh06.tar.bz2 \
 && bunzip2 -c 179mh06.tar.bz2 | tar -xv
WORKDIR /usr/local/src/tnn/179mh06
#
# fix for makefile bug
RUN sed -i "s:.*cp \$(MSGMSY)/msg.{usr,sys} \$(TNNMSG).*:#:g" contrib/msgmsy/makefile
#
# make
RUN make baseinstall
#
# fix for makefile bug - manual copy
RUN cp contrib/msgmsy/msg.usr /usr/local/tnn/msg/ \
 && cp contrib/msgmsy/msg.sys /usr/local/tnn/msg/
#
# disable all default config
WORKDIR /usr/local/tnn
RUN sed -i 's/on/off/g' tnn179.tnb \
 && sed -i 's/device/#device/g' tnn.ini \
 && sed -i 's/tnn_lockfile/#tnn_lockfile/g' tnn.ini \
 && sed -i 's/speed/#speed/g' tnn.ini \
 && sed -i 's/kisstype/#kisstype/g' tnn.ini \
 && sed -i 's/port/#port/g' tnn.ini
#
# set arguments to tnn179.pas
RUN sed -i "s/12345678901234567890123456789012345678901234567890123456789012345678901234567890/${pwsysop}/g" tnn179.pas \
 && sed -i "s/Geheim/${pwconsole}/g" tnn179.pas \
 && sed -i "s/Test/${nodeident}/g" tnn179.pas \
 && sed -i "s/XX0XX/${nodecall}/g" tnn179.pas
#
#
# setup ports from build args
# example:
# port1=<name>|<device>|<type>|[<speed>]|[<lockfile>]
# port1=/dev/ttyS0|1|9600|/var/lock/LCK..ttyS0
# port2=AX25IP|8
#
# type of devices/ports:
# 0 = KISS, 1 = SMACK, 2 = RMNC-KISS,
# 4 = Vanessa, 5 = SCC, 6 = TF (experimental!),
# 7 = IPX (1 IPX #port only),
# 8 = AX25IP (1 AX25IP #port only)
# 10 = Kernel-AX.25, 11 = DG1KJD Kernel-AX.25
# 12 = 6PACK
#
RUN /scripts/wizard_ports "${port1}" "${port2}" "${port3}" "${port4}" "${port5}" "${port6}" "${port7}" "${port8}" "${port9}" "${port10}" "${port11}" "${port12}" "${port13}" "${port14}" "${port15}"
#
# END: Install TNN
# =================================================================================

# Expose default port
EXPOSE 10093/udp

# Set the default command
WORKDIR /usr/local/tnn
#ENTRYPOINT ["/usr/bin/screen","-S","tnn","-D","-m","/usr/local/tnn/tnn"]
ENTRYPOINT ["/scripts/start_tnn"]

