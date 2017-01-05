FROM ubuntu:16.04
RUN apt-get update && apt-get install -yq wget curl git innoextract software-properties-common && apt-get clean
RUN wget http://files.jrsoftware.org/is/5/isetup-5.5.5-unicode.exe && innoextract -d /inno /isetup-5.5.5-unicode.exe

RUN curl -sL https://deb.nodesource.com/setup_5.x | bash - && \
    dpkg --add-architecture i386 && \
    add-apt-repository ppa:ubuntu-wine/ppa && \
    apt-get update && \
    apt-get install -y nodejs wine1.8 winetricks && \
    apt-get clean && \
    echo "%sudo ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers && \
    useradd -u 1000 -G users,sudo -d /user --shell /bin/bash -m user && \
    echo "secret\nsecret" | passwd user

RUN mkdir /opt/docker &&  wget --local-encoding=UTF-8 --no-check-certificate -O /opt/docker/InstallDocker.msi https://download.docker.com/win/stable/InstallDocker.msi

ADD ./id_rsa /user/.ssh/id_rsa

RUN chmod 600 /user/.ssh/*
RUN chown -R user:user /user/.ssh/*
RUN ssh-keyscan -t rsa github.com >> /user/.ssh/known_hosts

RUN npm install electron-packager -g

USER user
WORKDIR /user

# Clone electron into the docker container
RUN git clone git@github.com:codenvy/che-electron.git

WORKDIR /user/che-electron
RUN npm install
RUN npm run dist

WORKDIR /che
ENTRYPOINT ["wine", "/inno/app/ISCC.exe", "/Otarget"]
