FROM alpine:3.10

# Environment variables
ENV PATH="/mattermost/bin:${PATH}"
# ENV MM_VERSION=5.29.0
# ENV MM_INSTALL_TYPE=docker
# ENV MM_CONFIG="/mattermost/config/config.json"

# Build arguments
ARG PUID=1999
ARG PGID=1999
ARG MM_VERSION=5.29.0

# Install packages
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
	&& curl https://releases.mattermost.com/$MM_VERSION/mattermost-team-$MM_VERSION-linux-amd64.tar.gz?src=docker-app | tar -xvz \
	&& cp /mattermost/config/config.json /config.json.save \
	&& rm -rf /mattermost/config/config.json \
	&& addgroup -g ${PGID} mattermost \
	&& adduser -D -u ${PUID} -G mattermost -h /mattermost -D mattermost \
	&& chown -R mattermost:mattermost /mattermost /config.json.save /mattermost/plugins /mattermost/client/plugins \
	&& setcap cap_net_bind_service=+ep /mattermost/bin/mattermost

# Configure entrypoint
COPY entrypoint.sh /
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]

# Switch to user mattermost
USER mattermost

# Healthcheck to make sure container is ready
HEALTHCHECK CMD curl --fail http://localhost:8000 || exit 1

# Configure command & workdir
WORKDIR /mattermost
CMD ["mattermost"]

# Expose port 8000 of the container
EXPOSE 8000