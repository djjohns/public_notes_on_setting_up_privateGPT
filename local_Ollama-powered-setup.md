# Install on WSL Ubuntu 22.04 LTS:
### Sources:
[Private GPT docs](https://docs.privategpt.dev/installation/getting-started/installation#local-ollama-powered-setup-recommended)

[Installing PrivateGPT on WSL with GPU support](https://medium.com/@docteur_rs/installing-privategpt-on-wsl-with-gpu-support-5798d763aa31) *Note: This guide is outdated 2024/03/06. privateGPT installation changed with this* [commit](https://github.com/imartinez/privateGPT/commit/45f05711eb71ffccdedb26f37e680ced55795d44)

## Install WSL if you don't already have it:

[How to install Linux on Windows with WSL](https://learn.microsoft.com/en-us/windows/wsl/install)

## Updating Ubuntu

*Updating Ubuntu 22.04 LTS even if it's a fresh install is important.*

Get the latest packages and install build-essential package.

```bash
sudo apt-get update && sudo apt-get upgrade -y && sudo apt-get install build-essential -y
```

##  Clone the PrivateGPT repo

[privateGPT REPO](https://github.com/imartinez/privateGPT)

```bash
git clone https://github.com/imartinez/privateGPT
```

##  Setup python environment

To manage Python versions, we’ll use pyenv. Follow the commands below to install it and set up the Python environment:

```bash
sudo apt-get install git gcc make openssl libssl-dev libbz2-dev libreadline-dev libsqlite3-dev zlib1g-dev libncursesw5-dev libgdbm-dev libc6-dev zlib1g-dev libsqlite3-dev tk-dev libssl-dev openssl libffi-dev
curl https://pyenv.run | bash
export PATH="/home/$(whoami)/.pyenv/bin:$PATH"
```

Add PYENV_ROOT to our .bashrc file:

```bash
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

Reload the terminal.

```bash
source .bashrc
```

Install important missing pyenv stuff

```bash
sudo apt-get install lzma
sudo apt-get install liblzma-dev
```

Install Python 3.11 and set it as the global version:

```bash
pyenv install 3.11
pyenv global 3.11
pip install pip --upgrade
pyenv local 3.11
```

## Poetry Installation

Install poetry to manage dependencies:

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

Add the following line to your .bashrc:

```bash
export PATH="/home/<YOU USERNAME>/.local/bin:$PATH"
```

- *Replace <YOUR USERNAME> by your WSL username ($ whoami)*

Reload to get the new configuration.

```bash
source ~/.bashrc
poetry --version # should display something without errors
```

## Installing PrivateGPT Dependencies

### Local, Ollama-powered setup - RECOMMENDED

The easiest way to run PrivateGPT fully locally is to depend on Ollama for the LLM. Ollama provides local LLM and Embeddings super easy to install and use, abstracting the complexity of GPU support. It's the recommended setup for local development.

Go to ollama.ai and follow the instructions to install Ollama on your machine.

#### Install Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Once Ollama is installed, run:

```bash
ollama serve
```

Ollama should be running on [localhost:11434](http://127.0.0.1:11434/)

Open a new terminal, and down the model and embedding:
```bash
ollama pull mistral
ollama pull nomic-embed-text
```


Navigate to the PrivateGPT directory and install dependencies:

```bash
cd privateGPT
```

```bash
poetry install --extras "ui llms-ollama embeddings-ollama vector-stores-qdrant"
```

Once installed, you can run PrivateGPT. Make sure you have a working Ollama running locally before running the following command.

```bash
PGPT_PROFILES=ollama make run
```

PrivateGPT will use the already existing settings-ollama.yaml settings file, which is already configured to use Ollama LLM and Embeddings, and Qdrant. Review it and adapt it to your needs (different models, different Ollama port, etc.)

The UI will be available at http://localhost:8001


# Notes
I've had some trouble with the ollama api service not stopping, even with a kill PID command. The only solution I have found to work at the moment is to restart the host. Since we are using WSL that means a restarting the windows pc.

Once rebooted one can open a WSL Ubuntu 22.04 LTS terminal and start the ollama service with:

```bash
ollama serve
```

Then open a second WSL Ubuntu 22.04 LTS terminal and start privateGPT with:

```bash
PGPT_PROFILES=ollama make run
```