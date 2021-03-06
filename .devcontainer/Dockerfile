# This is the Dockerfile used to build the image hosted at:
# https://hub.docker.com/repository/docker/cryptolcourse/dev
# To use this Dockerfile directly, uncomment the appropriate line in
# devcontainer.json
FROM debian:buster as extras
RUN apt-get update \
    && apt-get install -y curl make libgmp-dev gperf gcc autoconf \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
# Necessary because Cryptol image doesn't contain mathsat
RUN curl -L https://mathsat.fbk.eu/download.php?file=mathsat-5.6.3-linux-x86_64.tar.gz | tar xz \
    && cp mathsat*/bin/mathsat /usr/local/bin \
    && rm -rf mathsat*
# Necessary because static yices build in the Cryptol image doesn't work on some systems
RUN curl -L https://yices.csl.sri.com/releases/2.6.2/yices-2.6.2-src.tar.gz | tar xz \
    && cd yices* && autoconf && ./configure && make \
    && cp build/*/bin/* /usr/local/bin \
    && cp build/*/bin/yices_smt2 /usr/local/bin/yices-smt2 \
    && cd .. && rm -rf yices*

FROM debian:buster
RUN apt-get update \
    && apt-get install -y wget unzip vim emacs-nox git curl dos2unix \
    && apt-get clean && rm -rf /var/lib/apt/lists/*
COPY --from=galoisinc/saw:0.6 /usr/local/bin /usr/local/bin
COPY --from=galoisinc/cryptol:2.10.0 /usr/local/bin /usr/local/bin
COPY --from=extras /usr/local/bin /usr/local/bin
RUN useradd -m -p '' cryptol && chown -R cryptol:cryptol /home/cryptol
USER cryptol
ENV LANG C.UTF-8
RUN echo 'export PS1="\$(pwd)> "' >> /home/cryptol/.bashrc

ENTRYPOINT ["/bin/bash"]
