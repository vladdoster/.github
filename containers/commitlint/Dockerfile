# syntax=docker/dockerfile:1
FROM node:24-trixie-slim AS npm
WORKDIR /app
RUN npm install --omit=peer --no-package-lock --no-audit --no-fund @commitlint/cli @commitlint/config-angular

FROM debian:trixie-slim AS git
RUN apt-get update \
 && apt-get install -y --no-install-recommends git \
 && lib=/usr/lib/$(uname -m)-linux-gnu \
 && install -D /usr/bin/git /out/usr/bin/git \
 && install -D -m 644 $lib/libpcre2-8.so.0 /out$lib/libpcre2-8.so.0

FROM gcr.io/distroless/nodejs24-debian13
LABEL org.opencontainers.image.source=https://github.com/vladdoster/.github
COPY --from=git /out/ /
COPY --from=npm /app/node_modules /app/node_modules
COPY .commitlintrc /app/
# The container user does not own the workspace. This setting lets git read it.
ENV GIT_CONFIG_COUNT=1 GIT_CONFIG_KEY_0=safe.directory GIT_CONFIG_VALUE_0=*
ENTRYPOINT ["/nodejs/bin/node", "/app/node_modules/@commitlint/cli/cli.js", "--config", "/app/.commitlintrc"]
