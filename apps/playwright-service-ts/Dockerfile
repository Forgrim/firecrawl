# Erste Stufe: Builder für die CRX-Dateien
FROM python:3.9-slim-buster AS crx_downloader

WORKDIR /crx_download

# Python Script hinzufügen
COPY download_crx.py .

# Extension IDs als Argumente
ARG UBLOCK_ID="cjpalhdlnbpafiamejdnhcphjbkeiagm"
ARG IDCAC_ID="edibdbjcniadpccecjdfdjjppcpchdlm"

# CRX-Dateien herunterladen
RUN python3 download_crx.py $UBLOCK_ID -o ublock.crx
RUN python3 download_crx.py $IDCAC_ID -o idcac.crx

# Verzeichnis für die CRX-Dateien erstellen
RUN mkdir /crx_files
# Heruntergeladene CRX-Dateien in das Verzeichnis kopieren
RUN mv *.crx /crx_files/

# ----------------

# Zweite Stufe: Die eigentliche Node.js Anwendung
FROM node:18-slim AS app

WORKDIR /usr/src/app
COPY package*.json ./
RUN npm install

# CRX-Dateien aus der ersten Stufe kopieren
COPY --from=crx_downloader /crx_files /usr/src/app/extensions

# Copy source
COPY . .

# Install Playwright dependencies
RUN npx playwright install --with-deps

RUN npm run build

ARG PORT
ENV PORT=${PORT}

EXPOSE ${PORT}

CMD [ "npm", "start" ]
