#####################
## Base Image Info ##
##################################
##    Distro Name: Ubuntu linux ##
##  Default Shell: /bin/busybox ##
##################################
FROM docker.io/library/ubuntu:latest
## Image MetaData ##
LABEL MAINTAINER="ph4n70m.nuk3r@gmail.com"
## Update package lists; Upgrade packages; Install BASH, cURL, and Nginx. ##
RUN export DEBIAN_FRONTEND="noninteractive" \
    && apt-get update \
    && apt-get upgrade -y \
    && apt-get install -y bash curl nginx \
    && apt autoremove -y \
    && apt-get clean -y
## Define literal constants first. ##
ENV NON_ROOT_USER="kitchen-sink" \
    SHELL_="/bin/bash" \
    YACRON_ARCH="x86_64-unknown-linux-gnu" \
    YACRON_VERSION="0.19.0" \
    YACRON_GITHUB_RELEASES="https://github.com/gjcarneiro/yacron/releases" \
    YACRON_BIN_PATH="/usr/bin/yacron" \
    YACRON_CONFIG_DIR="/etc/yacron" \
    YACRON_CONFIG_FILE="yacron.conf" \
    YACRON_PORT="8081" \
    YACRON_PORT_PROTOCOL="tcp" \
    NGINX_CACHE_DIR="/var/cache/nginx" \
    NGINX_CONFIG_DIR="/etc/nginx" \
    NGINX_CONFIG_FILE="nginx.conf" \
    NGINX_LOG_DIR="/var/lib/nginx" \
    NGINX_TEMP_DIR="/tmp/nginx" \
    NGINX_WEB_ROOT_DIR="/var/www/kitchen-sink" \
    NGINX_PORT="8080" \
    NGINX_PORT_PROTOCOL="tcp"
## Declare variables referencing literal constants. ##
ENV NON_ROOT_GROUP="${NON_ROOT_USER}" \
    YACRON_ARTEFACT="yacron-${YACRON_VERSION}-${YACRON_ARCH}" \
    YACRON_CONFIG_PATH="${YACRON_CONFIG_DIR}/${YACRON_CONFIG_FILE}" \
    YACRON_HEALTHCHECK="wget http://localhost:${YACRON_PORT}" \
    NGINX_CONFIG_PATH="${NGINX_CONFIG_DIR}/${NGINX_CONFIG_FILE}" \
    NGINX_WEB_DOC_DIR="${NGINX_WEB_ROOT_DIR}/public/doc" \
    NGINX_WEB_HTML_DIR="${NGINX_WEB_ROOT_DIR}/static/html" \
    NGINX_HEALTHCHECK="wget http://localhost:${NGINX_PORT}"
## Declare variables referencing variables which reference literal constants. ##
ENV YACRON_BIN_DL_URL="${YACRON_GITHUB_RELEASES}/download/${YACRON_VERSION}/${YACRON_ARTEFACT}" \
    YACRON_RUN_COMMAND="yacron -c ${YACRON_CONFIG_PATH}" \
    NGINX_RUN_COMMAND="nginx -c ${NGINX_CONFIG_PATH}"
## Download YaCron binary from GitHub to PATH and make executable. ##
RUN curl --connect-timeout 10 --max-time 30 --silent --location --output "${YACRON_BIN_PATH}" "${YACRON_BIN_DL_URL}" \
    && chmod +x "${YACRON_BIN_PATH}"
## Create non-root group + user. ##
RUN groupadd "${NON_ROOT_GROUP}" \
    && useradd --gid "${NON_ROOT_GROUP}" --no-create-home --shell "${SHELL_}" "${NON_ROOT_USER}"
## Create dirs and set permissions. ##
RUN mkdir -p "${NGINX_CACHE_DIR}" "${NGINX_CONFIG_DIR}" "${NGINX_WEB_DOC_DIR}" "${NGINX_WEB_HTML_DIR}" "${NGINX_TEMP_DIR}" "${NGINX_LOG_DIR}" \
    && chown -R ${NON_ROOT_USER}:0 "${NGINX_CACHE_DIR}" "${NGINX_CONFIG_DIR}" "${NGINX_WEB_DOC_DIR}" "${NGINX_WEB_HTML_DIR}" "${NGINX_TEMP_DIR}" "${NGINX_LOG_DIR}" \
    && chmod -R g+w "${NGINX_CACHE_DIR}" "${NGINX_CONFIG_DIR}" "${NGINX_WEB_DOC_DIR}" "${NGINX_WEB_HTML_DIR}" "${NGINX_TEMP_DIR}" "${NGINX_LOG_DIR}"
## Copy Nginx config into image. ##
COPY --chown="${NON_ROOT_USER}:0" "./resources/buildtime/config/nginx.conf" "${NGINX_CONFIG_PATH}"
COPY --chown="${NON_ROOT_USER}:0" "./resources/buildtime/config/yacron.yaml" "${YACRON_CONFIG_PATH}"
COPY --chown="${NON_ROOT_USER}:0" "./resources/buildtime/public/doc/example.txt" "${NGINX_WEB_DOC_DIR}/"
COPY --chown="${NON_ROOT_USER}:0" "./resources/buildtime/static/html/404.html" "${NGINX_WEB_HTML_DIR}/"
## Container runtime settings. ##
USER "${NON_ROOT_USER}:${NON_ROOT_GROUP}"
WORKDIR /
ENTRYPOINT ${YACRON_RUN_COMMAND} &  ${NGINX_RUN_COMMAND}
HEALTHCHECK --interval=30s --timeout=10s --retries=3 --start-period=5s CMD ${YACRON_HEALTHCHECK} && ${NGINX_HEALTHCHECK}
