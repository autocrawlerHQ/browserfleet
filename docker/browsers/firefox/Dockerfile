FROM browsergrid/base:latest

LABEL org.opencontainers.image.description="Firefox browser for Browsergrid"
LABEL org.opencontainers.image.version="${BROWSER_VERSION}"
LABEL browsergrid.browser.version="${BROWSER_VERSION}"
LABEL browsergrid.playwright.version="${PLAYWRIGHT_VERSION}"

ARG PLAYWRIGHT_VERSION=latest
ARG BROWSER_VERSION=latest

RUN apt-get update && apt-get install -y sudo curl jq && \
    echo "browseruser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    rm -rf /var/lib/apt/lists/*

RUN npm i playwright@${PLAYWRIGHT_VERSION}
RUN npx playwright install --with-deps firefox

# Create Firefox profile directory with proper permissions
RUN mkdir -p ${HOME}/firefox-profile && \
    mkdir -p ${HOME}/firefox-media && \
    chown -R browseruser:browseruser ${HOME}/firefox-profile && \
    chown -R browseruser:browseruser ${HOME}/firefox-media && \
    chmod -R 755 ${HOME}/firefox-profile && \
    chmod -R 755 ${HOME}/firefox-media

COPY docker/browsers/firefox/start.sh /start.sh
COPY docker/browsers/firefox/docker-healthcheck /usr/local/bin/docker-healthcheck
RUN chmod +x /start.sh /usr/local/bin/docker-healthcheck
RUN chown browseruser:browseruser /start.sh

HEALTHCHECK --interval=5s --timeout=3s --retries=3 CMD ["docker-healthcheck"]

# Run Firefox as browseruser while keeping the container's main process as root
CMD ["su", "-c", "/start.sh", "browseruser"]
