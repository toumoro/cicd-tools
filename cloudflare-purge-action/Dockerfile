FROM alpine:latest

LABEL version="1.0.0"
LABEL organization="Toumoro"
LABEL homepage="https://toumoro.com/"
LABEL maintainer="Benoit Verret <benoit.verret@toumoro.com>"
LABEL repository="https://github.com/toumoto/cicd-tools/tree/master/cloudflare-purge-action"

RUN apk update && apk add openssl curl

ADD entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]