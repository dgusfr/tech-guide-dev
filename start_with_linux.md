# Start with Linux — Ambiente de Desenvolvimento no Ubuntu

Este guia é um passo a passo para preparar um Ubuntu para desenvolvimento backend, principalmente com Python, FastAPI, Docker, banco de dados e ferramentas de terminal.

A regra deste documento é simples:

- cada comando fica em um bloco separado;
- execute os comandos na ordem;
- não pule os comandos de verificação;
- quando aparecer `nano`, você deve editar o arquivo, salvar e sair.

No `nano`, para salvar e sair:

1. Aperte `CTRL + O`
2. Aperte `Enter`
3. Aperte `CTRL + X`

---

## Índice

1. [Capítulo 1 — Atualizar o Ubuntu](#capitulo-1)
2. [Capítulo 2 — Instalar pacotes básicos de desenvolvimento](#capitulo-2)
3. [Capítulo 3 — Instalar e configurar Git](#capitulo-3)
4. [Capítulo 4 — Instalar Zsh e Oh My Zsh](#capitulo-4)
5. [Capítulo 5 — Instalar Google Chrome](#capitulo-5)
6. [Capítulo 6 — Instalar Visual Studio Code](#capitulo-6)
7. [Capítulo 7 — Instalar Docker Engine e Docker Compose](#capitulo-7)
8. [Capítulo 8 — Instalar DBeaver Community](#capitulo-8)
9. [Capítulo 9 — Instalar Postman](#capitulo-9)
10. [Capítulo 10 — Instalar PostgreSQL Client](#capitulo-10)
11. [Capítulo 11 — Instalar Redis CLI](#capitulo-11)
12. [Capítulo 12 — Instalar pyenv](#capitulo-12)
13. [Capítulo 13 — Instalar Poetry](#capitulo-13)
14. [Capítulo 14 — Instalar NVM e Node.js LTS](#capitulo-14)
15. [Capítulo 15 — Instalar GitHub CLI](#capitulo-15)
16. [Capítulo 16 — Verificação final do ambiente](#capitulo-16)
99. [Referências bibliográficas](#referencias-bibliograficas)

---

<a id="capitulo-1"></a>
# Capítulo 1 — Atualizar o Ubuntu

Antes de instalar qualquer ferramenta, atualize a lista de pacotes do Ubuntu e depois atualize os pacotes já instalados.

## Comando 1

```bash
sudo apt update
```

Esse comando atualiza a lista de pacotes disponíveis nos repositórios configurados na sua máquina.

## Comando 2

```bash
sudo apt upgrade -y
```

Esse comando atualiza os pacotes já instalados no seu Ubuntu.

## Comando 3

```bash
sudo apt autoremove -y
```

Esse comando remove dependências antigas que não são mais necessárias.

---

<a id="capitulo-2"></a>
# Capítulo 2 — Instalar pacotes básicos de desenvolvimento

Esses pacotes são usados por várias ferramentas de desenvolvimento. Eles evitam erros comuns, como `curl: command not found`, `git: command not found`, erro de chave GPG, erro ao compilar Python pelo pyenv e falta de ferramentas de terminal.

## Comando 1

```bash
sudo apt install git -y
```

Instala o Git, usado para versionamento de código.

## Comando 2

```bash
sudo apt install curl -y
```

Instala o curl, usado para baixar scripts e arquivos pela linha de comando.

## Comando 3

```bash
sudo apt install wget -y
```

Instala o wget, outra ferramenta usada para baixar arquivos pelo terminal.

## Comando 4

```bash
sudo apt install build-essential -y
```

Instala compiladores e ferramentas de build, úteis para Python, Node e bibliotecas nativas.

## Comando 5

```bash
sudo apt install openssh-client -y
```

Instala o cliente SSH, necessário para autenticação com GitHub via chave SSH.

## Comando 6

```bash
sudo apt install unzip -y
```

Instala suporte para descompactar arquivos `.zip`.

## Comando 7

```bash
sudo apt install gnupg -y
```

Instala ferramentas GPG, usadas para validar chaves de repositórios externos.

## Comando 8

```bash
sudo apt install ca-certificates -y
```

Instala certificados confiáveis para conexões HTTPS.

## Comando 9

```bash
sudo apt install lsb-release -y
```

Instala uma ferramenta usada para identificar a versão da distribuição Linux.

## Comando 10

```bash
sudo apt install software-properties-common -y
```

Instala utilitários para gerenciar repositórios no Ubuntu.

## Comando 11

```bash
sudo apt install tmux -y
```

Instala o tmux, que permite manter sessões de terminal abertas mesmo se você fechar a janela.

## Comando 12

```bash
tmux -V
```

Verifica se o tmux foi instalado corretamente.

---

<a id="capitulo-3"></a>
# Capítulo 3 — Instalar e configurar Git

Git é a ferramenta principal para versionamento de código. Você usa Git para clonar projetos, criar branches, versionar alterações e enviar código para GitHub, GitLab ou Bitbucket.

Se você já instalou o Git no capítulo anterior, aqui você só confirma a instalação e configura seu nome e e-mail.

## Comando 1

```bash
git --version
```

Mostra a versão instalada do Git.

## Comando 2

```bash
git config --global user.name "Seu Nome"
```

Configura o nome que aparecerá nos commits.

Exemplo:

```text
git config --global user.name "Diego Gustavo Franco"
```

## Comando 3

```bash
git config --global user.email "seu-email@exemplo.com"
```

Configura o e-mail que aparecerá nos commits.

Exemplo:

```text
git config --global user.email "diegogustavo.fr@gmail.com"
```

## Comando 4

```bash
git config --global init.defaultBranch main
```

Define `main` como nome padrão da branch inicial em novos repositórios.

## Comando 5

```bash
git config --global --list
```

Mostra suas configurações globais do Git.

---

<a id="capitulo-4"></a>
# Capítulo 4 — Instalar Zsh e Oh My Zsh

Zsh é um shell mais confortável que o Bash para uso diário. Oh My Zsh é um framework que deixa o Zsh mais amigável, com temas, plugins e atalhos.

Importante: o erro `zsh: command not found: curl` acontece quando o curl ainda não foi instalado. Por isso o curl foi instalado antes, no Capítulo 2.

## Comando 1

```bash
sudo apt install zsh -y
```

Instala o Zsh.

## Comando 2

```bash
zsh --version
```

Verifica se o Zsh foi instalado.

## Comando 3

```bash
chsh -s $(which zsh)
```

Define o Zsh como shell padrão do seu usuário.

Depois desse comando, feche e abra o terminal.

Se aparecer a tela `zsh-newuser-install`, digite:

```text
0
```

Isso cria um `.zshrc` mínimo e impede que essa tela apareça de novo.

## Comando 4

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Instala o Oh My Zsh usando o script oficial.

## Comando 5

```bash
echo $SHELL
```

Mostra qual shell está sendo usado.

O esperado é algo parecido com:

```text
/usr/bin/zsh
```

## Comando 6

```bash
nano ~/.zshrc
```

Abre o arquivo de configuração do Zsh.

Procure a linha dos plugins e deixe assim:

```text
plugins=(git)
```

Salve e saia do nano.

## Comando 7

```bash
source ~/.zshrc
```

Recarrega as configurações do Zsh sem precisar fechar o terminal.

---

<a id="capitulo-5"></a>
# Capítulo 5 — Instalar Google Chrome

Google Chrome é útil para desenvolvimento web, testes de APIs, documentação, Google Meet, WhatsApp Web e ferramentas de desenvolvimento do navegador.

## Comando 1

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

Baixa o pacote `.deb` oficial do Google Chrome para Ubuntu/Debian.

## Comando 2

```bash
sudo apt install ./google-chrome-stable_current_amd64.deb -y
```

Instala o Google Chrome e suas dependências.

## Comando 3

```bash
rm google-chrome-stable_current_amd64.deb
```

Remove o instalador depois que o Chrome já foi instalado.

## Comando 4

```bash
google-chrome --version
```

Verifica se o Chrome foi instalado corretamente.

---

<a id="capitulo-6"></a>
# Capítulo 6 — Instalar Visual Studio Code

Visual Studio Code é o editor principal para programar. Ele funciona bem com Python, FastAPI, Docker, Git, Markdown, YAML, JSON e extensões para desenvolvimento backend.

## Comando 1

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Garante que a pasta de chaves APT exista.

## Comando 2

```bash
wget -qO microsoft.asc https://packages.microsoft.com/keys/microsoft.asc
```

Baixa a chave pública da Microsoft.

## Comando 3

```bash
sudo gpg --dearmor -o /etc/apt/keyrings/packages.microsoft.gpg microsoft.asc
```

Converte e salva a chave da Microsoft no formato usado pelo APT.

## Comando 4

```bash
rm microsoft.asc
```

Remove o arquivo temporário da chave.

## Comando 5

```bash
echo "deb [arch=amd64,arm64,armhf signed-by=/etc/apt/keyrings/packages.microsoft.gpg] https://packages.microsoft.com/repos/code stable main" | sudo tee /etc/apt/sources.list.d/vscode.list
```

Adiciona o repositório oficial do VS Code no APT.

## Comando 6

```bash
sudo apt update
```

Atualiza a lista de pacotes, agora incluindo o repositório do VS Code.

## Comando 7

```bash
sudo apt install code -y
```

Instala o Visual Studio Code.

## Comando 8

```bash
code --version
```

Verifica se o VS Code foi instalado corretamente.

---

<a id="capitulo-7"></a>
# Capítulo 7 — Instalar Docker Engine e Docker Compose

Docker permite rodar serviços em containers, como PostgreSQL, Redis, APIs, workers e ambientes de desenvolvimento isolados. Para backend, é uma das ferramentas mais importantes.

Este passo instala o Docker Engine oficial pelo repositório da Docker.

## Comando 1

```bash
sudo apt remove docker.io docker-compose docker-compose-v2 docker-doc podman-docker containerd runc -y
```

Remove pacotes antigos ou conflitantes, se existirem.

Se aparecer que alguns pacotes não existem, não tem problema.

## Comando 2

```bash
sudo apt install ca-certificates curl -y
```

Garante os pacotes necessários para baixar a chave oficial da Docker via HTTPS.

## Comando 3

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Cria a pasta de chaves do APT, caso ela ainda não exista.

## Comando 4

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```

Baixa a chave GPG oficial da Docker.

## Comando 5

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Ajusta a permissão da chave para o APT conseguir ler.

## Comando 6

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo \"${UBUNTU_CODENAME:-$VERSION_CODENAME}\") stable" | sudo tee /etc/apt/sources.list.d/docker.list
```

Adiciona o repositório oficial da Docker no Ubuntu.

## Comando 7

```bash
sudo apt update
```

Atualiza a lista de pacotes, agora incluindo o repositório da Docker.

## Comando 8

```bash
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```

Instala Docker Engine, Docker CLI, containerd, Buildx e Docker Compose Plugin.

## Comando 9

```bash
sudo docker run hello-world
```

Testa se o Docker está funcionando usando `sudo`.

## Comando 10

```bash
sudo usermod -aG docker $USER
```

Adiciona seu usuário ao grupo `docker`, para você poder usar Docker sem `sudo`.

## Comando 11

```bash
newgrp docker
```

Ativa o grupo `docker` na sessão atual do terminal.

## Comando 12

```bash
docker run hello-world
```

Testa se o Docker funciona sem `sudo`.

## Comando 13

```bash
docker compose version
```

Verifica se o Docker Compose Plugin foi instalado corretamente.

---

<a id="capitulo-8"></a>
# Capítulo 8 — Instalar DBeaver Community

DBeaver Community é uma ferramenta visual para acessar bancos de dados. É útil para PostgreSQL, MySQL, MariaDB, SQLite, SQL Server, Oracle e outros.

Como o repositório APT do DBeaver pode gerar erro de chave GPG em alguns casos, a forma mais simples no Ubuntu Desktop é instalar pelo Snap.

## Comando 1

```bash
snap version
```

Verifica se o Snap está disponível no seu Ubuntu.

Se esse comando funcionar, siga para o Comando 3.

Se aparecer `snap: command not found`, execute o Comando 2.

## Comando 2

```bash
sudo apt install snapd -y
```

Instala o Snap, caso ele não esteja presente.

## Comando 3

```bash
sudo snap install dbeaver-ce
```

Instala o DBeaver Community.

## Comando 4

```bash
dbeaver-ce
```

Abre o DBeaver pelo terminal.

Você também pode abrir pelo menu do Ubuntu procurando por `DBeaver`.

---

<a id="capitulo-9"></a>
# Capítulo 9 — Instalar Postman

Postman é usado para testar APIs. Com ele você cria collections, configura autenticação, salva requisições, testa rotas e compartilha ambientes.

## Comando 1

```bash
sudo snap install postman
```

Instala o Postman pelo Snap.

## Comando 2

```bash
postman
```

Abre o Postman pelo terminal.

Você também pode abrir pelo menu do Ubuntu procurando por `Postman`.

---

<a id="capitulo-10"></a>
# Capítulo 10 — Instalar PostgreSQL Client

PostgreSQL Client instala o comando `psql`, usado para conectar em bancos PostgreSQL pelo terminal.

Você pode usar o `psql` mesmo se o PostgreSQL estiver rodando em Docker, em outro servidor ou na nuvem.

## Comando 1

```bash
sudo apt install postgresql-client -y
```

Instala o cliente PostgreSQL.

## Comando 2

```bash
psql --version
```

Verifica se o `psql` foi instalado corretamente.

Exemplo de uso futuro:

```text
psql -h localhost -p 5432 -U postgres -d nome_do_banco
```

---

<a id="capitulo-11"></a>
# Capítulo 11 — Instalar Redis CLI

Redis CLI instala o comando `redis-cli`, usado para testar conexão com Redis, inspecionar chaves, publicar mensagens e debugar aplicações que usam Redis.

Você pode usar o `redis-cli` mesmo se o Redis estiver rodando via Docker.

## Comando 1

```bash
sudo apt install redis-tools -y
```

Instala as ferramentas de linha de comando do Redis.

## Comando 2

```bash
redis-cli --version
```

Verifica se o Redis CLI foi instalado corretamente.

Exemplo de uso futuro:

```text
redis-cli -h localhost -p 6379
```

---

<a id="capitulo-12"></a>
# Capítulo 12 — Instalar pyenv

pyenv serve para instalar e gerenciar várias versões do Python sem bagunçar o Python do sistema operacional.

Para desenvolvimento backend com Python, FastAPI, SQLAlchemy, Alembic e Poetry, pyenv é muito útil.

## Comando 1

```bash
sudo apt install make -y
```

Instala o `make`, usado durante a compilação do Python.

## Comando 2

```bash
sudo apt install libssl-dev -y
```

Instala dependências de SSL, necessárias para o Python compilar suporte a HTTPS.

## Comando 3

```bash
sudo apt install zlib1g-dev -y
```

Instala dependência de compressão usada pelo Python.

## Comando 4

```bash
sudo apt install libbz2-dev -y
```

Instala suporte a bzip2.

## Comando 5

```bash
sudo apt install libreadline-dev -y
```

Instala suporte ao readline, usado no terminal interativo do Python.

## Comando 6

```bash
sudo apt install libsqlite3-dev -y
```

Instala suporte ao SQLite no Python.

## Comando 7

```bash
sudo apt install libncursesw5-dev -y
```

Instala dependência de terminal usada por algumas bibliotecas.

## Comando 8

```bash
sudo apt install xz-utils -y
```

Instala utilitários para arquivos `.xz`.

## Comando 9

```bash
sudo apt install tk-dev -y
```

Instala suporte a Tkinter.

## Comando 10

```bash
sudo apt install libxml2-dev -y
```

Instala dependência para bibliotecas que trabalham com XML.

## Comando 11

```bash
sudo apt install libxmlsec1-dev -y
```

Instala dependência usada por bibliotecas de XML Security.

## Comando 12

```bash
sudo apt install libffi-dev -y
```

Instala suporte a FFI, usado por várias bibliotecas Python.

## Comando 13

```bash
sudo apt install liblzma-dev -y
```

Instala suporte a LZMA.

## Comando 14

```bash
curl -fsSL https://pyenv.run | bash
```

Instala o pyenv usando o instalador do projeto.

## Comando 15

```bash
nano ~/.zshrc
```

Abre o arquivo de configuração do Zsh.

No final do arquivo, cole este trecho:

```text
# pyenv
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init - zsh)"
```

Salve e saia do nano.

## Comando 16

```bash
source ~/.zshrc
```

Recarrega o Zsh para ativar o pyenv.

## Comando 17

```bash
pyenv --version
```

Verifica se o pyenv foi instalado corretamente.

## Comando 18

```bash
pyenv install --list
```

Lista as versões de Python disponíveis para instalação.

## Comando 19

```bash
pyenv install 3.12.10
```

Instala o Python 3.12.10 pelo pyenv.

## Comando 20

```bash
pyenv global 3.12.10
```

Define o Python 3.12.10 como versão global do seu usuário.

## Comando 21

```bash
python --version
```

Verifica qual Python está ativo.

---

<a id="capitulo-13"></a>
# Capítulo 13 — Instalar Poetry

Poetry é uma ferramenta para gerenciar dependências e ambientes virtuais em projetos Python.

Com Poetry, você usa `pyproject.toml` em vez de depender só de `requirements.txt`.

## Comando 1

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

Instala o Poetry usando o instalador oficial.

## Comando 2

```bash
nano ~/.zshrc
```

Abre o arquivo de configuração do Zsh.

No final do arquivo, cole este trecho:

```text
# Poetry
export PATH="$HOME/.local/bin:$PATH"
```

Salve e saia do nano.

## Comando 3

```bash
source ~/.zshrc
```

Recarrega o Zsh para reconhecer o comando `poetry`.

## Comando 4

```bash
poetry --version
```

Verifica se o Poetry foi instalado corretamente.

## Comando 5

```bash
poetry config virtualenvs.in-project true
```

Configura o Poetry para criar o ambiente virtual dentro do projeto, na pasta `.venv`.

Isso facilita o uso com VS Code.

## Comando 6

```bash
poetry config --list
```

Mostra as configurações atuais do Poetry.

---

<a id="capitulo-14"></a>
# Capítulo 14 — Instalar NVM e Node.js LTS

NVM é o gerenciador de versões do Node.js. Mesmo sendo backend Python, Node.js é útil para ferramentas de frontend, CLIs, documentação, linters e projetos full stack.

## Comando 1

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.5/install.sh | bash
```

Instala o NVM usando o script oficial.

## Comando 2

```bash
source ~/.zshrc
```

Recarrega o Zsh.

Se o comando `nvm` ainda não funcionar, feche e abra o terminal.

## Comando 3

```bash
command -v nvm
```

Verifica se o NVM foi carregado corretamente.

O esperado é aparecer:

```text
nvm
```

## Comando 4

```bash
nvm install --lts
```

Instala a versão LTS mais recente do Node.js.

## Comando 5

```bash
nvm use --lts
```

Ativa a versão LTS do Node.js.

## Comando 6

```bash
nvm alias default lts/*
```

Define a versão LTS como padrão para novos terminais.

## Comando 7

```bash
node --version
```

Verifica a versão instalada do Node.js.

## Comando 8

```bash
npm --version
```

Verifica a versão instalada do npm.

---

<a id="capitulo-15"></a>
# Capítulo 15 — Instalar GitHub CLI

GitHub CLI instala o comando `gh`, usado para autenticar no GitHub, listar repositórios, clonar projetos, abrir pull requests, ver issues e trabalhar com GitHub direto pelo terminal.

## Comando 1

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Garante que a pasta de chaves APT exista.

## Comando 2

```bash
wget -nv -O githubcli-archive-keyring.gpg https://cli.github.com/packages/githubcli-archive-keyring.gpg
```

Baixa a chave oficial do GitHub CLI.

## Comando 3

```bash
sudo cp githubcli-archive-keyring.gpg /etc/apt/keyrings/githubcli-archive-keyring.gpg
```

Copia a chave para a pasta usada pelo APT.

## Comando 4

```bash
sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg
```

Ajusta a permissão da chave.

## Comando 5

```bash
rm githubcli-archive-keyring.gpg
```

Remove o arquivo temporário da chave.

## Comando 6

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list
```

Adiciona o repositório oficial do GitHub CLI.

## Comando 7

```bash
sudo apt update
```

Atualiza a lista de pacotes.

## Comando 8

```bash
sudo apt install gh -y
```

Instala o GitHub CLI.

## Comando 9

```bash
gh --version
```

Verifica se o GitHub CLI foi instalado corretamente.

## Comando 10

```bash
gh auth login
```

Inicia o login no GitHub pelo terminal.

Sugestão de escolhas durante o login:

```text
GitHub.com
HTTPS ou SSH, conforme seu uso
Login with a web browser
```

---

<a id="capitulo-16"></a>
# Capítulo 16 — Verificação final do ambiente

Depois de instalar tudo, rode os comandos abaixo para conferir o ambiente.

## Comando 1

```bash
git --version
```

Confere o Git.

## Comando 2

```bash
zsh --version
```

Confere o Zsh.

## Comando 3

```bash
echo $SHELL
```

Confere o shell padrão.

## Comando 4

```bash
google-chrome --version
```

Confere o Google Chrome.

## Comando 5

```bash
code --version
```

Confere o VS Code.

## Comando 6

```bash
docker --version
```

Confere o Docker.

## Comando 7

```bash
docker compose version
```

Confere o Docker Compose.

## Comando 8

```bash
python --version
```

Confere o Python ativo.

## Comando 9

```bash
pyenv --version
```

Confere o pyenv.

## Comando 10

```bash
poetry --version
```

Confere o Poetry.

## Comando 11

```bash
node --version
```

Confere o Node.js.

## Comando 12

```bash
npm --version
```

Confere o npm.

## Comando 13

```bash
psql --version
```

Confere o PostgreSQL Client.

## Comando 14

```bash
redis-cli --version
```

Confere o Redis CLI.

## Comando 15

```bash
gh --version
```

Confere o GitHub CLI.

## Comando 16

```bash
tmux -V
```

Confere o tmux.

---

<a id="referencias-bibliograficas"></a>
# Referências bibliográficas

GIT. *1.5 Getting Started - Installing Git*. Disponível em: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git. Acesso em: 07 jun. 2026.

TMUX. *Installing — tmux Wiki*. Disponível em: https://github.com/tmux/tmux/wiki/Installing. Acesso em: 07 jun. 2026.

OH MY ZSH. *Oh My Zsh — Basic Installation*. Disponível em: https://ohmyz.sh/. Acesso em: 07 jun. 2026.

MICROSOFT. *Installing Visual Studio Code on Linux*. Disponível em: https://code.visualstudio.com/docs/setup/linux. Acesso em: 07 jun. 2026.

GOOGLE. *Fazer o download do instalador do Chrome*. Disponível em: https://support.google.com/chrome/a/answer/9025926?hl=pt-BR. Acesso em: 07 jun. 2026.

DOCKER. *Install Docker Engine on Ubuntu*. Disponível em: https://docs.docker.com/engine/install/ubuntu/. Acesso em: 07 jun. 2026.

DBEAVER. *Download DBeaver Community*. Disponível em: https://dbeaver.io/download/. Acesso em: 07 jun. 2026.

SNAPCRAFT. *DBeaver Community*. Disponível em: https://snapcraft.io/dbeaver-ce. Acesso em: 07 jun. 2026.

POSTMAN. *Install and update Postman*. Disponível em: https://learning.postman.com/docs/getting-started/installation/installation-and-updates/. Acesso em: 07 jun. 2026.

SNAPCRAFT. *Postman*. Disponível em: https://snapcraft.io/postman. Acesso em: 07 jun. 2026.

POSTGRESQL. *Linux downloads — Ubuntu*. Disponível em: https://www.postgresql.org/download/linux/ubuntu/. Acesso em: 07 jun. 2026.

REDIS. *Install Redis on Linux*. Disponível em: https://redis.io/docs/latest/operate/oss_and_stack/install/archive/install-redis/install-redis-on-linux/. Acesso em: 07 jun. 2026.

PYENV. *Simple Python Version Management: pyenv*. Disponível em: https://github.com/pyenv/pyenv. Acesso em: 07 jun. 2026.

POETRY. *Poetry Documentation — Installation*. Disponível em: https://python-poetry.org/docs/. Acesso em: 07 jun. 2026.

NVM. *Node Version Manager — Installing and Updating*. Disponível em: https://github.com/nvm-sh/nvm#installing-and-updating. Acesso em: 07 jun. 2026.

NODE.JS. *Download Node.js*. Disponível em: https://nodejs.org/en/download. Acesso em: 07 jun. 2026.

GITHUB CLI. *GitHub CLI — Linux Installation*. Disponível em: https://github.com/cli/cli/blob/trunk/docs/install_linux.md. Acesso em: 07 jun. 2026.

GITHUB DOCS. *GitHub CLI quickstart*. Disponível em: https://docs.github.com/en/github-cli/github-cli/quickstart. Acesso em: 07 jun. 2026.
