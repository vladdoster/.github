# syntax=docker/dockerfile:1
FROM debian:trixie-slim AS build
RUN apt-get update \
 && apt-get install -y --no-install-recommends python3-pip zsh \
 && python3 -m pip install --no-cache-dir --no-compile --root-user-action=ignore --target /opt/mdformat \
      linkify-it-py mdformat mdformat-config mdformat-gfm mdformat-shfmt mdformat-toc \
 && lib=/usr/lib/$(uname -m)-linux-gnu \
 && install -D /usr/bin/zsh /out/usr/bin/zsh \
 && install -D -m 644 $lib/libcap.so.2 /out$lib/libcap.so.2 \
 && cp -a $lib/zsh /out$lib/

FROM gcr.io/distroless/python3-debian13
LABEL org.opencontainers.image.source=https://github.com/vladdoster/.github
COPY --from=koalaman/shellcheck:stable /bin/shellcheck /usr/local/bin/
COPY --from=mvdan/shfmt:v3 /bin/shfmt /usr/local/bin/
COPY --from=build /out/ /
COPY --from=build /opt/mdformat /opt/mdformat
ENV PATH=/opt/mdformat/bin:/usr/local/bin:/usr/bin:/bin \
    PYTHONPATH=/opt/mdformat \
    PYTHONDONTWRITEBYTECODE=1
# The image has no other shell. Give each check as one zsh command string.
ENTRYPOINT ["zsh", "-o", "extendedglob", "-c"]
