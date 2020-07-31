FROM python:3.8-alpine

LABEL version="1.0.0"
LABEL organization="Toumoro"
LABEL homepage="https://toumoro.com/"
LABEL maintainer="Benoit Verret <benoit.verret@toumoro.com>"
LABEL repository="https://github.com/toumoto/cicd-tools/tree/master/slack-messaging-action"

ADD . /app
WORKDIR /app

RUN pip install --target=/app requests

COPY entrypoint.py /entrypoint.py
ENV PYTHONPATH /app

CMD ["/entrypoint.py"]
ENTRYPOINT [ "python" ]
