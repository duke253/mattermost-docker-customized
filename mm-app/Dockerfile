FROM alpine:3.10

# Environment variables
ENV PATH="/mattermost/bin:${PATH}"
ENV MM_VERSION=5.29.0
ENV MM_INSTALL_TYPE=docker
ENV MM_CONFIG="/mattermost/config/config.json"
ENV DB_HOST=epdbhu50ul1if3edm0li.auto.internal
ENV DB_PORT_NUMBER=5432
ENV MM_DBNAME=mattermostdb
ENV MM_USERNAME=mmdbuser01
ENV MM_PASSWORD=QaZ(77)zAq
ENV MM_SQLSETTINGS_DATASOURCE="postgres://$MM_USERNAME:$ENCODED_PASSWORD@$DB_HOST:$DB_PORT_NUMBER/$MM_DBNAME?sslmode=disable&connect_timeout=10"

# Build argument to set Mattermost edition
ARG edition=team
ARG PUID=1999
ARG PGID=1999
ARG MM_BINARY=


# Install some needed packages
RUN apk add --no-cache \
	ca-certificates \
	curl \
	jq \
	libc6-compat \
	libffi-dev \
	libcap \
	linux-headers \
	mailcap \
	netcat-openbsd \
	xmlsec-dev \
	tzdata \
	&& rm -rf /tmp/*

# Get Mattermost
RUN mkdir -p /mattermost/data /mattermost/plugins /mattermost/client/plugins \
	&& if [ ! -z "$MM_BINARY" ]; then curl $MM_BINARY | tar -xvz ; \
		elif [ "$edition" = "team" ] ; then curl https://releases.mattermost.com/$MM_VERSION/mattermost-team-$MM_VERSION-linux-amd64.tar.gz?src=docker-app | tar -xvz ; \
		else curl https://releases.mattermost.com/$MM_VERSION/mattermost-$MM_VERSION-linux-amd64.tar.gz?src=docker-app | tar -xvz ; fi \
	&& cp /mattermost/config/config.json /config.json.save \
	&& rm -rf /mattermost/config/config.json \
	&& addgroup -g ${PGID} mattermost \
	&& adduser -D -u ${PUID} -G mattermost -h /mattermost -D mattermost \
	&& chown -R mattermost:mattermost /mattermost /config.json.save /mattermost/plugins /mattermost/client/plugins \
	&& setcap cap_net_bind_service=+ep /mattermost/bin/mattermost

USER mattermost

#Healthcheck to make sure container is ready
HEALTHCHECK CMD curl --fail http://localhost:8000 || exit 1

# Configure entrypoint and command
WORKDIR /mattermost
COPY entrypoint.sh /
RUN chmod +x entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["mattermost"]

# Expose port 8000 of the container
EXPOSE 8000

# Declare volumes for mount point directories
VOLUME ["/mattermost/data", "/mattermost/logs", "/mattermost/config", "/mattermost/plugins", "/mattermost/client/plugins"]
