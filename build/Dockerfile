FROM alpine:latest
LABEL maintainer="taichunmin@gmail.com"

RUN set -ex; \
  echo "http://dl-cdn.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories; \
  apk add --no-cache openssh autossh

CMD ["/bin/sh"]