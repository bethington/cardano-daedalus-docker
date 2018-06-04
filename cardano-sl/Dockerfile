# Build:
# docker build -t bethington/cardano-sl .
#
# Run:
# docker run -it bethington/cardano-sl
#
# Compose:
# docker-compose up -d

FROM ubuntu:16.04
MAINTAINER Ben Ethington <benaminde@gmail.com>

# Install basic requirements and create cardano user
RUN apt-get update && apt-get install -y bzip2 curl git
RUN apt-get clean && rm -fr /var/lib/apt/lists/*
RUN useradd -ms /bin/bash cardano

# Install nix as cardano user
RUN mkdir /nix && chown cardano /nix
RUN mkdir -p /etc/nix
COPY nix.conf /etc/nix/
USER cardano
# The nix install script needs USER to be set
ENV USER cardano
RUN curl https://nixos.org/nix/install | sh

# Clone and build cardano-sl
WORKDIR /home/cardano
RUN git clone --branch master https://github.com/input-output-hk/cardano-sl.git
WORKDIR /home/cardano/cardano-sl
RUN . /home/cardano/.nix-profile/etc/profile.d/nix.sh && \
    nix-build -A cardano-sl --cores 0 --max-jobs 2 --out-link master
RUN . /home/cardano/.nix-profile/etc/profile.d/nix.sh && \
    nix-build -A connectScripts.mainnetWallet -o connect-to-mainnet

#CMD bash -C 'bash'
CMD ./connect-to-mainnet
