#############################################################################################
####                   UBUNTU, PYTHON, POETRY, JUPYTER DEV CONTAINER                     ####
#############################################################################################

FROM ubuntu:22.04 
#Python 3.10.12 is default in this distro

ARG DEBIAN_FRONTEND=noninteractive

USER root

# Install system requirements
RUN apt-get update \
    && apt-get install -y \
        git \
        curl \
        zsh \
        fonts-powerline \
        python3-pip \
        nodejs \
        npm \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Install Oh My Zsh with powerlevel10k theme and plugins
RUN sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" "" --unattended \
    && chsh -s $(which zsh) \
    && git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k \
    && sed -i 's/ZSH_THEME=".*"/ZSH_THEME="powerlevel10k\/powerlevel10k"/g' ~/.zshrc \
    && git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting \
    && git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions \
    && sed -i 's/plugins=(git)/plugins=(git zsh-syntax-highlighting zsh-autosuggestions)/g' ~/.zshrc

# Python3 and pip3 as default
RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 1 \
    && update-alternatives --install /usr/bin/pip pip /usr/bin/pip3 1

# Install and configure poetry
RUN pip install poetry \
    && poetry config virtualenvs.in-project false \
    && poetry config virtualenvs.path "/root/.cache/pypoetry/virtualenvs/poetry-env-py3.10" 

# Install python dependencies from poetry.lock
WORKDIR /workspaces/ubuntu-python-poetry-jupyter-devcontainer    
COPY pyproject.toml poetry.lock /workspaces/ubuntu-python-poetry-jupyter-devcontainer/
RUN poetry install

# Create a new Jupyter kernel using the Poetry environment
RUN poetry run python -m ipykernel install --user --name=poetry-env --display-name "Python (Poetry)"

CMD poetry run jupyter

