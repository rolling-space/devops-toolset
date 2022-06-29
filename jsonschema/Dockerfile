FROM ghcr.io/mtsmfm/devfiles/ruby:3.1-node-16

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get install -y python3-dev pip3 python3 \
    && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/* \

RUN npm install -g quicktype postman-to-openapi
RUN pip install openapi2jsonschema

RUN mkdir -p /bench/input /bench/output /bench/current
VOLUME ["/bench/input", "/bench/output", "/bench/current"]

WORKDIR /bench

USER 10001

