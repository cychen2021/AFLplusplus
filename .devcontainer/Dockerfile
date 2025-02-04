FROM mcr.microsoft.com/devcontainers/base:noble

RUN adduser --disabled-password --gecos '' ubuntu
RUN adduser ubuntu sudo
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER ubuntu
ENV HOME=/home/ubuntu
ARG BUILD_DIR=/tmp/build
RUN mkdir ${BUILD_DIR}
WORKDIR ${BUILD_DIR}

RUN rm -rf /var/lib/apt/lists/* && sudo apt-get update && sudo apt-get install -y \
    software-properties-common
RUN sudo add-apt-repository ppa:deadsnakes/ppa
RUN sudo apt-get update
RUN sudo apt-get install -y python3.13 pipx
RUN pipx ensurepath
RUN pipx install poetry==1.8.4 xonsh==0.18.3
RUN pipx inject xonsh click==8.1.7
RUN sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.13 100

RUN case $(uname -m) in \
		x86_64) wget -O llvm.tar.xz "https://github.com/llvm/llvm-project/releases/download/llvmorg-16.0.2/clang+llvm-16.0.2-x86_64-linux-gnu-ubuntu-22.04.tar.xz" ;; \
		aarch64) wget -O llvm.tar.xz "https://github.com/llvm/llvm-project/releases/download/llvmorg-16.0.2/clang+llvm-16.0.2-aarch64-linux-gnu.tar.xz" ;; \	
	esac;
RUN sudo tar -xJvf llvm.tar.xz -C /usr --strip-components=1
RUN rm llvm.tar.xz

ENV PATH="/home/ubuntu/.local/bin:${PATH}"
RUN poetry config virtualenvs.in-project true
ENV VIRTUAL_ENV_DISABLE_PROMPT=1

RUN rm -rvf ${BUILD_DIR}
RUN sudo userdel -r vscode # The vscode user takes the 1000 UID, which is the default for the host ubuntu user

RUN (type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) \
	&& sudo mkdir -p -m 755 /etc/apt/keyrings \
	&& wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	&& sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
	&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
	&& sudo apt-get update \
	&& sudo apt-get install gh libzstd-dev libtinfo-dev libxml2-dev -y
RUN sudo apt-get install -y git-lfs vim python3-pylsp bear
