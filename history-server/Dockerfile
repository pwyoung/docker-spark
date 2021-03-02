FROM bde2020/spark-base:3.0.2-hadoop3.2

LABEL maintainer="Gezim Sejdiu <g.sejdiu@gmail.com>, Giannis Mouchakis <gmouchakis@gmail.com>"

COPY history-server.sh /

# https://spark.apache.org/docs/latest/monitoring.html#environment-variables

EXPOSE 18081

CMD ["/bin/bash", "/history-server.sh"]
