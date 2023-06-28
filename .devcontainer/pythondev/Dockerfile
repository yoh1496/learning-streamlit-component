FROM python:3.10-buster

ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m -s /bin/bash $USERNAME \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Create bash_history
RUN SNIPPET="export PROMPT_COMMAND='history -a' && export HISTFILE=/commandhistory/.bash_history" \
    && mkdir /commandhistory \
    && touch /commandhistory/.bash_history \
    && chown -R $USERNAME /commandhistory \
    && echo $SNIPPET >> "/home/$USERNAME/.bashrc"

# Create workspace directory
RUN mkdir /workspace \
    && chown -R $USERNAME /workspace

RUN apt-get update \
    && apt-get install -y locales \
    && locale-gen ja_JP.UTF-8 \
    && apt-get install -y socat \
    && apt-get install -y vim screen wget

ENV LC_ALL=C.UTF-8

# ********************************************************
# * Anything else you want to do like clean up goes here *
# ********************************************************

# [Optional] Set the default user. Omit if you want to keep the default as root.
USER $USERNAME

RUN mkdir -p /home/${USERNAME}/opt \
    && cd /home/${USERNAME}/opt \
    && wget https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash \
    && wget https://raw.githubusercontent.com/git/git/master/contrib/completion/git-prompt.sh \
    && echo "source ~/opt/git-completion.bash" >> ~/.bashrc \
    && echo "source ~/opt/git-prompt.sh" >> ~/.bashrc \
    && echo "source ~/opt/add-git-branch-to-ps1.bash" >> ~/.bashrc

COPY --chown=${USERNAME}:${USERNAME} add-git-branch-to-ps1.bash /home/${USERNAME}/opt/add-git-branch-to-ps1.bash

RUN mkdir -p \
        /home/${USERNAME}/.vscode-server/extensions \
        /home/${USERNAME}/.vscode-server-insiders/extensions \
    && chown -R $USERNAME \
        /home/${USERNAME}/.vscode-server \
        /home/${USERNAME}/.vscode-server-insiders

ARG COMMIT
ENV COMMIT ${COMMIT}

RUN echo 'export PATH=$PATH:$HOME/.local/bin' >> ~/.bashrc \
    && pip install -U streamlit

# install nvm and nodejs
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash && \
    bash -c '\
    . "$HOME/.nvm/nvm.sh" && nvm install 16 && nvm use v16 && nvm alias default v16 \
    '
