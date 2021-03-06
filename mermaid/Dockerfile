# Package the Node.js project into a single binary
FROM node:12.16.2-alpine3.11 as builder

# Workaround: https://github.com/nodejs/docker-node/issues/813#issuecomment-407339011
# Error: could not get uid/gid
# [ 'nobody', 0 ]
RUN npm config set unsafe-perm true

RUN npm install -g pkg pkg-fetch

ENV NODE node10
ENV PLATFORM alpine
ENV ARCH x64
RUN /usr/local/bin/pkg-fetch ${NODE} ${PLATFORM} ${ARCH}

WORKDIR /app

COPY package.json package-lock.json /app/
COPY src /app/src

RUN PUPPETEER_SKIP_CHROMIUM_DOWNLOAD=1 npm ci

RUN /usr/local/bin/pkg --targets ${NODE}-${PLATFORM}-${ARCH} . -o app.bin

# Create the image
FROM alpine:3.11.6

COPY --from=builder /app/app.bin /node/bin/mermaid
COPY assets /app/assets
ENV KROKI_MERMAID_PAGE_URL=file:///app/assets/index.html

RUN apk add --no-cache --update chromium
ENV PUPPETEER_EXECUTABLE_PATH=/usr/lib/chromium/chrome

EXPOSE 8002

ENTRYPOINT ["/node/bin/mermaid"]
