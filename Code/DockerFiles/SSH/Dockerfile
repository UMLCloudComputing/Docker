FROM ubuntu:latest
RUN apt-get update && apt install openssh-server -y
RUN apt install nano -y
COPY entrypoint.sh /
WORKDIR /
ENTRYPOINT ["/bin/bash", "entrypoint.sh"]