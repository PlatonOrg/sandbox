FROM debian:bookworm

LABEL maintainer="thomas.saillard2@univ-eiffel.fr"

ENV DEBIAN_FRONTEND=noninteractive

# Installing base requirements
RUN apt-get update && apt-get install -y --no-install-recommends \
    sudo \
    gnupg2 \
    libasound2 \
    libasound2-data \
    locales \
    wget \
    ca-certificates \
    build-essential \
    libssl-dev \
    openssl \
    unzip \
    python3 \
    git \
    ghc \
    clang \
    ocaml-nox \
    ocaml \
    perl \
    perl-doc \
    python3-distutils \
    python3-pip \
    postgresql \
    libbsd-dev \
    sqlite3 \
    r-base \
    && apt-get clean \
    && apt-get autoclean \
    && apt-get autoremove \
    && rm -rf /var/lib/apt/lists/*

# Set python3 as default
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 2

# Installing pip3 and python's requirements
ADD requirements.txt .
RUN pip3 --no-cache-dir install --upgrade --break-system-packages pip setuptools wheel &&\
    pip3 --no-cache-dir install --break-system-packages -r requirements.txt

# Installing nvm, node and npm
ENV NODE_VERSION=v20.13.1
ADD package.json .
RUN mkdir -p /usr/local/nvm
ENV NVM_DIR=/usr/local/nvm
RUN wget -qO- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash \
    && . $NVM_DIR/nvm.sh \
    && nvm install $NODE_VERSION \
    && npm i -D @swc/cli @swc/core \
    && npm i --save-dev -g
RUN mkdir -p /var/www/.npm/_logs
RUN touch /var/www/.npm/_update-notifier-last-checked
RUN chmod 755 -R /var/www/.npm/
RUN chown -R www-data:www-data /var/www/.npm/

ENV PATH="/usr/local/nvm/versions/node/${NODE_VERSION}/bin:${PATH}"

# Installing Java 22 
RUN wget --progress=dot:giga https://download.oracle.com/java/22/archive/jdk-22_linux-$(arch | sed s/x86_64/x64/)_bin.tar.gz -O /tmp/jdk-22_linux_bin.targz &&\
    mkdir -p jdk-22-oracle &&\
    tar -xzvf /tmp/jdk-22_linux_bin.targz -C jdk-22-oracle --strip-components=1 &&\
    mkdir -p /usr/lib/jvm/ &&\
    cp -r jdk-22-oracle /usr/lib/jvm/ &&\
    rm /tmp/jdk-22_linux_bin.targz &&\
    ls -la /usr/lib/jvm/
RUN update-alternatives --install /usr/bin/java java /usr/lib/jvm/jdk-22-oracle/bin/java 3
RUN update-alternatives --install /usr/bin/javac javac /usr/lib/jvm/jdk-22-oracle/bin/javac 3

RUN mkdir -p /utils
# Download Junit standalone jar
RUN wget --progress=dot:giga https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/1.10.3/junit-platform-console-standalone-1.10.3.jar -O /utils/junit-platform-console-standalone.jar
# Download JavaParser jar
RUN wget --progress=dot:giga https://repo1.maven.org/maven2/com/github/javaparser/javaparser-core/3.26.1/javaparser-core-3.26.1.jar -O /utils/javaparser-core.jar

# Cleaning /tmp
RUN rm -Rf /tmp/*

RUN yes | cpan -l > /dev/null 2>&1

ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8
ENV LC_TYPE=en_US.UTF-8
RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
RUN locale-gen "en_US.UTF-8" && dpkg-reconfigure locales

WORKDIR /home/student
RUN chmod -R a+rwx /home/student

CMD ["bash"]