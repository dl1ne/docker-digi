# Set the base image
FROM debian

# Environment
ENV SHELL=/bin/bash

# Dockerfile author / maintainer
MAINTAINER Simon Brecht <simon@brecht.email>

# Add Scripts to Image
ADD . /scripts

# Update application repository list and install common Linux tools
RUN apt-get update && apt-get install -y git wget curl \
 && apt-get install ax25* libax25* make gcc bzip2 screen g++ -y

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
# END: Install OpenBCM
# =================================================================================

ENTRYPOINT ["/scripts/start_bcm"]
