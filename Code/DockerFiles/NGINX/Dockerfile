FROM ubuntu:latest
ENV DEBIAN_FRONTEND noninteractive
RUN apt -y update
RUN apt install nginx -y
WORKDIR /
COPY entrypoint.sh .
ENTRYPOINT ["/bin/bash", "entrypoint.sh"]
 