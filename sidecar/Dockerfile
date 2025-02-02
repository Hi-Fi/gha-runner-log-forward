FROM ubuntu:24.04

RUN apt-get update -qqy \
    && apt-get install xtail \
    && rm -rf /var/lib/apt/lists/* /var/cache/apt/*

CMD [ "/bin/bash", "-c", "xtail _diag/pages" ]
