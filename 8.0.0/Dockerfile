# © Copyright IBM Corporation 2015.
#
# All rights reserved. This program and the accompanying materials
# are made available under the terms of the Eclipse Public License v1.0
# which accompanies this distribution, and is available at
# http://www.eclipse.org/legal/epl-v10.html

FROM ubuntu:12.04

MAINTAINER Arthur Barr <arthur.barr@uk.ibm.com>

RUN export DEBIAN_FRONTEND=noninteractive && \
	MQ_URL=http://public.dhe.ibm.com/ibmdl/export/pub/software/websphere/messaging/mqadv/mqadv_dev80_linux_x86-64.tar.gz  && \
	MQ_PACKAGES="MQSeriesRuntime-*.rpm MQSeriesServer-*.rpm MQSeriesMsg*.rpm MQSeriesJRE*.rpm MQSeriesGSKit*.rpm" && \
	echo "mq:8.0.0.0" > /etc/debian_chroot && \
	apt-get update -y && \
	apt-get install -y curl tar bash rpm bc && \
	mkdir -p /tmp/mq && \
	cd /tmp/mq && \
	curl -LO $MQ_URL && \
	tar -xvf ./*.tar.gz && \
	groupadd --gid 1414 mqm && \
	useradd --uid 1414 --gid mqm --home-dir /var/mqm mqm && \
	usermod -G mqm root && \
    ./mqlicense.sh -text_only -accept && \
	rpm -ivh --force-debian $MQ_PACKAGES && \
	rm -rf /tmp/mq && \
	# Bypass defect in the "mqconfig" script
	sed -i -e "s;CheckShellDefaultOptions$;#CheckShellDefaultOptions;g" /opt/mqm/bin/mqconfig

COPY *.sh /usr/local/bin/
COPY *.mqsc /etc/mqm/

# Make sure the MQ environment is available for "docker exec" under non-interactive Bash
ENV BASH_ENV=/usr/local/bin/mq-env.sh

RUN chmod +x /usr/local/bin/*.sh

EXPOSE 1414

VOLUME /var/mqm

ENTRYPOINT ["mq.sh"]
