FROM python:3.8-alpine
LABEL maintainer="Benoit Verret TM <benoit.verret@toumoro.com>"

LABEL org.label-schema.vendor="Toumoro"
LABEL org.label-schema.schema-version="1.0"
LABEL org.label-schema.url="https://toumoro.com/"
LABEL org.label-schema.description="Interactive messaging services for Slack"
LABEL org.label-schema.name="kerberosmorphy/toumoro-slack-messaging"
LABEL org.label-schema.vcs-url="https://github.com/tm-bverret/toumoro-slack-messaging"

ADD . /app
WORKDIR /app

RUN pip install --target=/app requests

COPY entrypoint.py /entrypoint.py
ENV PYTHONPATH /app

CMD ["/entrypoint.py"]
ENTRYPOINT [ "python" ]
