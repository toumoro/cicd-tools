FROM appleboy/drone-ssh:1.6.1-linux-amd64

LABEL version="1.0.0"
LABEL organization="Toumoro"
LABEL homepage="https://toumoro.com/"
LABEL maintainer="Benoit Verret <benoit.verret@toumoro.com>"
LABEL repository="https://github.com/toumoto/cicd-tools/tree/master/cloudflare-purge-action"

ADD entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]