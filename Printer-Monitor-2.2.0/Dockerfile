FROM node:22-bookworm-slim

RUN apt-get update \
    && apt-get install -y --no-install-recommends iputils-ping tini \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

COPY --chown=node:node package.json package-lock.json ./
RUN npm ci --omit=dev --no-audit --no-fund \
    && npm cache clean --force

COPY --chown=node:node server.js printer-monitor.html set-password.js ./
COPY --chown=node:node 1.ico 2.png panel.png ./

ENV NODE_ENV=production \
    CONF=/data/printers.json

USER node
EXPOSE 8899

ENTRYPOINT ["tini", "--"]
CMD ["node", "server.js"]
