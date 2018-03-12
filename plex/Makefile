PLEX_VERSION?=latest
DATA_DIR?=/data
PUID=911
PGID=911
VERSION = "0.1.0"

all: dir update deploy

dir:
	@mkdir -p $(DATA_DIR)/plex \
		$(DATA_DIR)/transmission \
		$(DATA_DIR)/couchpotato \
		$(DATA_DIR)/transcode \
		$(DATA_DIR)/downloads \
		$(DATA_DIR)/sickrage \
		$(DATA_DIR)/sonarr \
		$(DATA_DIR)/radarr \
		$(DATA_DIR)/movies \
		$(DATA_DIR)/watch \
		$(DATA_DIR)/tv \
		|| echo "### TRY SUDO ###"
	@chown -R $(PUID):$(PGID) $(DATA_DIR) || echo "### TRY SUDO ###"

update:
	@docker pull linuxserver/plex:latest
	@docker pull linuxserver/transmission:latest
	@docker pull linuxserver/couchpotato:latest
	@docker pull linuxserver/sickrage:latest
	@docker pull linuxserver/sonarr:latest
	@docker pull linuxserver/radarr:latest

deploy:
	@docker stack deploy -c plex-stack.yml plex

destroy:
	@docker stack rm plex

.PHONY: all dir docker test update deploy destroy
