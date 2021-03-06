FROM ghcr.io/mtsmfm/devfiles/ruby:3.1-node-16 AS base

USER 0

ENV HOME=/bench/home

RUN for i in bundle .bundle bin current home src init shared mnt; do mkdir -p /bench/$i; done

WORKDIR /bench/home

### Setup user for build execution and application runtime
ENV APP_ROOT=/bench \
  USER_NAME=bench \
  USER_UID=10001
ENV APP_HOME=${APP_ROOT}/src \
PATH=$PATH:${APP_ROOT}/bin

RUN mkdir -p ${APP_ROOT}/bin &&  \
  chmod -R u+x ${APP_ROOT}/bin && \
  useradd -l -u ${USER_UID} -r -g 0 -d ${APP_ROOT} -s /sbin/nologin -c "${USER_NAME} user" ${USER_NAME} && \
  chown -R ${USER_UID}:0 ${APP_ROOT} && \
  chmod -R g=u ${APP_ROOT}

RUN git clone https://github.com/Bash-it/bash-it.git ~/.bash_it \
  && ~/.bash_it/install.sh
RUN git clone https://github.com/asdf-vm/asdf.git ~/.asdf && \
echo "source $HOME/.asdf/asdf.sh" >> ~/.bashrc && \
echo "source $HOME/.asdf/completions/asdf.bash" >> ~/.bashrc && \
echo "export PATH=$PATH:$HOME/.arkade/bin/" >> ~/.bashrc
RUN bash -lc "source $HOME/.asdf/asdf.sh; asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git"
RUN bash -lc "source $HOME/.asdf/asdf.sh; asdf plugin add oc"

ENV PATH=$PATH:$HOME/.arkade/bin/ \
BASH_IT_THEME=''
RUN curl -sLS https://get.arkade.dev | sh
RUN arkade get kubectl kubecm oh-my-posh helm kustomize && \
  echo 'eval "$(oh-my-posh init bash)"' >> ~/.bashrc

RUN git clone --depth=1 https://github.com/romkatv/gitstatus.git ~/gitstatus && \
echo 'source ~/gitstatus/gitstatus.prompt.sh' >> ~/.bashrc

VOLUME ["/bench/current", "/bench/mnt", "/bench/init"]

FROM base AS builder

RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
  && apt-get install -y python3-dev pip python3 python3-setuptools \
  && apt-get autoremove -y && apt-get clean -y && rm -rf /var/lib/apt/lists/*

RUN npm install -g quicktype postman-to-openapi
RUN pip install openapi2jsonschema

RUN mkdir -p /project/input /project/output /project/current
VOLUME ["/project/input", "/project/output", "/project/current"]

ENV HOME=/bench/home

ENV PATH=$PATH:$HOME/.arkade/bin/ \
    BASH_IT_THEME='' \
    APP_ROOT=/bench \
    USER_NAME=bench \
    USER_UID=10001 \
    APP_HOME=${APP_ROOT}/src \
    PATH=$PATH:${APP_ROOT}/bin

WORKDIR /project

RUN chown -R 10001:0 /bench /project

CMD ["sleep", "infinity"]
SHELL ["/bin/bash"]

#USER 10001
