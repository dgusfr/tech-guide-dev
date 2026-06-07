# Start With Linux — Ambiente de Desenvolvimento no Ubuntu

## Índice

1. Capítulo 1 — Antes de começar
2. Capítulo 2 — Atualizar o sistema
3. Capítulo 3 — Ferramentas básicas de terminal e desenvolvimento
4. Capítulo 4 — Git
5. Capítulo 5 — Zsh e Oh My Zsh
6. Capítulo 6 — Tmux
7. Capítulo 7 — Google Chrome
8. Capítulo 8 — Visual Studio Code
9. Capítulo 9 — Docker Engine e Docker Compose Plugin
10. Capítulo 10 — DBeaver Community
11. Capítulo 11 — Dependências para Python e pyenv
12. Capítulo 12 — Poetry
13. Capítulo 13 — Postman ou Insomnia
14. Capítulo 14 — Redis CLI
15. Capítulo 15 — PostgreSQL Client
16. Capítulo 16 — NVM, Node.js LTS e npm
17. Capítulo 17 — GitHub CLI
18. Capítulo 18 — Checklist final de validação
99. Referências bibliográficas

---

# Capítulo 1 — Antes de começar

Este arquivo é um guia prático para preparar um Ubuntu para desenvolvimento backend, principalmente com Python, FastAPI, bancos de dados, Docker, Git e ferramentas de API.

A ideia é seguir os comandos na ordem correta e entender o que cada um faz.

Observações importantes:

- Sempre que aparecer `sudo`, o Ubuntu pode pedir a sua senha.
- Quando o terminal pedir confirmação com `[Y/n]`, digite `y` e pressione Enter.
- Se algum pacote já estiver instalado, o Ubuntu apenas avisará que ele já está na versão mais recente.
- Depois de mudar shell, instalar Docker ou configurar variáveis no `.zshrc`, pode ser necessário fechar e abrir o terminal.

---

# Capítulo 2 — Atualizar o sistema

Antes de instalar qualquer ferramenta, atualize a lista de pacotes e os pacotes já instalados no sistema.

## Comando 1

```bash
sudo apt update
```

Ele atualiza a lista de pacotes disponíveis nos repositórios configurados na sua máquina Linux. Ele não instala nem atualiza programas sozinho; apenas sincroniza a lista de versões disponíveis.

## Comando 2

```bash
sudo apt upgrade -y
```

Ele atualiza os pacotes já instalados no sistema para versões mais recentes disponíveis nos repositórios. O `-y` confirma automaticamente a instalação.

---

# Capítulo 3 — Ferramentas básicas de terminal e desenvolvimento

Esses pacotes são a base para quase qualquer ambiente de desenvolvimento no Ubuntu.

## Comando 1

```bash
sudo apt install -y git curl wget build-essential openssh-client zsh tmux unzip gnupg ca-certificates lsb-release software-properties-common
```

Ele instala ferramentas essenciais:

- `git`: controle de versão.
- `curl`: faz requisições e baixa arquivos via terminal.
- `wget`: baixa arquivos via terminal.
- `build-essential`: compiladores e ferramentas básicas de build.
- `openssh-client`: permite usar SSH, inclusive com GitHub.
- `zsh`: shell alternativo ao Bash.
- `tmux`: mantém sessões de terminal abertas em segundo plano.
- `unzip`: descompacta arquivos `.zip`.
- `gnupg`: usado para chaves GPG de repositórios.
- `ca-certificates`: certificados para conexões HTTPS confiáveis.
- `lsb-release`: mostra informações da distribuição Linux.
- `software-properties-common`: utilitários para gerenciar repositórios.

## Comando 2

```bash
git --version && curl --version && wget --version && zsh --version && tmux -V
```

Ele verifica se algumas das ferramentas foram instaladas corretamente.

---

# Capítulo 4 — Git

Git é a ferramenta de versionamento de código mais usada no desenvolvimento de software. Você usa Git para clonar projetos, criar branches, registrar commits e trabalhar com GitHub, GitLab ou Bitbucket.

Se você já instalou o pacote básico do capítulo anterior, o Git provavelmente já está instalado.

## Comando 1

```bash
git --version
```

Verifica se o Git está instalado e mostra a versão.

## Comando 2

```bash
git config --global user.name "Seu Nome"
```

Define o nome que aparecerá nos seus commits.

Exemplo:

```bash
git config --global user.name "Diego Gustavo Franco"
```

## Comando 3

```bash
git config --global user.email "seu-email@email.com"
```

Define o e-mail associado aos seus commits.

Exemplo:

```bash
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

Lista suas configurações globais do Git.

---

# Capítulo 5 — Zsh e Oh My Zsh

Zsh é um shell alternativo ao Bash. Ele é bastante usado por desenvolvedores por causa de autocomplete melhorado, temas, plugins e customização.

Oh My Zsh é um framework que facilita a configuração do Zsh.

## Comando 1

```bash
sudo apt install -y zsh
```

Instala o Zsh no Ubuntu.

## Comando 2

```bash
zsh --version
```

Verifica se o Zsh foi instalado corretamente.

## Comando 3

```bash
chsh -s $(which zsh)
```

Define o Zsh como shell padrão do seu usuário. Depois desse comando, feche e abra o terminal, ou reinicie a sessão do Ubuntu.

## Comando 4

```bash
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Instala o Oh My Zsh usando o script oficial do projeto.

## Comando 5

```bash
nano ~/.zshrc
```

Abre o arquivo de configuração do Zsh.

Procure a linha:

```bash
plugins=(git)
```

Ela ativa o plugin do Git no Oh My Zsh.

Para salvar no Nano:

1. Pressione `Ctrl + O`.
2. Pressione Enter.
3. Pressione `Ctrl + X`.

## Comando 6

```bash
source ~/.zshrc
```

Recarrega as configurações do Zsh sem precisar fechar o terminal.

---

# Capítulo 6 — Tmux

Tmux é um multiplexador de terminal. Ele permite criar várias sessões, janelas e painéis dentro do terminal.

É muito útil para desenvolvimento, servidores, Docker, logs e tarefas longas.

## Comando 1

```bash
sudo apt install -y tmux
```

Instala o tmux.

## Comando 2

```bash
tmux -V
```

Verifica a versão instalada.

## Comando 3

```bash
tmux
```

Abre uma nova sessão tmux.

Atalhos básicos dentro do tmux:

- `Ctrl + B`, depois `%`: divide a tela verticalmente.
- `Ctrl + B`, depois `"`: divide a tela horizontalmente.
- `Ctrl + B`, depois seta direcional: muda de painel.
- `Ctrl + B`, depois `d`: sai da sessão sem encerrá-la.

Para voltar a uma sessão aberta:

```bash
tmux attach
```

---

# Capítulo 7 — Google Chrome

Google Chrome é útil para desenvolvimento web, testes de aplicações, DevTools, debug de front-end e uso diário.

## Comando 1

```bash
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
```

Baixa o pacote `.deb` oficial do Google Chrome para Debian/Ubuntu.

## Comando 2

```bash
sudo apt install ./google-chrome-stable_current_amd64.deb -y
```

Instala o Google Chrome a partir do arquivo baixado. O `apt` também resolve dependências automaticamente.

## Comando 3

```bash
google-chrome --version
```

Verifica se o Chrome foi instalado corretamente.

## Comando 4

```bash
google-chrome
```

Abre o Google Chrome pelo terminal.

---

# Capítulo 8 — Visual Studio Code

Visual Studio Code é o editor principal para desenvolvimento. Ele é muito usado com Python, FastAPI, Docker, Git, Markdown, APIs e extensões.

## Comando 1

```bash
wget -O code.deb "https://code.visualstudio.com/sha/download?build=stable&os=linux-deb-x64"
```

Baixa o pacote `.deb` oficial do VS Code.

## Comando 2

```bash
sudo apt install ./code.deb -y
```

Instala o VS Code no Ubuntu.

## Comando 3

```bash
code --version
```

Verifica se o comando `code` está disponível no terminal.

## Comando 4

```bash
code .
```

Abre a pasta atual no VS Code.

---

# Capítulo 9 — Docker Engine e Docker Compose Plugin

Docker permite rodar aplicações em containers. Para desenvolvimento backend, ele é muito útil para subir bancos como PostgreSQL, Redis, MySQL e serviços auxiliares sem instalar tudo diretamente no sistema.

Docker Compose permite subir múltiplos containers a partir de um arquivo `docker-compose.yml` ou `compose.yml`.

## Comando 1

```bash
sudo apt update
```

Atualiza a lista de pacotes.

## Comando 2

```bash
sudo apt install -y ca-certificates curl
```

Garante que o sistema tenha certificados HTTPS e `curl`, necessários para adicionar o repositório oficial do Docker.

## Comando 3

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Cria o diretório onde a chave GPG do Docker será armazenada.

## Comando 4

```bash
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```

Baixa a chave GPG oficial do Docker.

## Comando 5

```bash
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Permite que o `apt` leia a chave GPG do Docker.

## Comando 6

```bash
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Adiciona o repositório oficial do Docker ao Ubuntu.

## Comando 7

```bash
sudo apt update
```

Atualiza a lista de pacotes novamente, agora incluindo o repositório do Docker.

## Comando 8

```bash
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

Instala Docker Engine, Docker CLI, runtime de containers, Buildx e Docker Compose Plugin.

## Comando 9

```bash
sudo docker run hello-world
```

Testa se o Docker está funcionando.

## Comando 10

```bash
sudo usermod -aG docker $USER
```

Adiciona seu usuário ao grupo `docker`, permitindo rodar Docker sem `sudo`. Depois desse comando, reinicie o computador ou faça logout/login.

## Comando 11

```bash
docker --version && docker compose version
```

Verifica as versões do Docker e do Docker Compose.

---

# Capítulo 10 — DBeaver Community

DBeaver Community é um cliente gráfico para bancos de dados. Ele ajuda a visualizar tabelas, rodar SQL, conectar em PostgreSQL, MySQL, MariaDB, SQLite, SQL Server e outros bancos.

Como no seu caso o repositório `apt` do DBeaver deu erro de chave GPG, a instalação via Snap é a mais simples.

## Comando 1

```bash
sudo snap install dbeaver-ce
```

Instala o DBeaver Community pelo Snap.

## Comando 2

```bash
dbeaver-ce
```

Abre o DBeaver pelo terminal.

## Observação

Se o DBeaver precisar acessar chaves SSH para conectar em bancos via túnel SSH, talvez seja necessário liberar permissões no Ubuntu Software ou conectar interfaces do Snap.

---

# Capítulo 11 — Dependências para Python e pyenv

pyenv permite instalar e alternar entre várias versões do Python sem depender apenas da versão que vem no Ubuntu.

Antes de instalar o pyenv, instale as dependências de compilação do Python.

## Comando 1

```bash
sudo apt update
```

Atualiza a lista de pacotes.

## Comando 2

```bash
sudo apt install -y make build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev curl git libncursesw5-dev xz-utils tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev
```

Instala bibliotecas e ferramentas necessárias para compilar versões do Python com pyenv.

## Comando 3

```bash
curl -fsSL https://pyenv.run | bash
```

Instala o pyenv usando o instalador recomendado pelo projeto.

## Comando 4

```bash
cat <<'PYENV_CONFIG' >> ~/.zshrc

# pyenv
export PYENV_ROOT="$HOME/.pyenv"
[[ -d $PYENV_ROOT/bin ]] && export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init - zsh)"
PYENV_CONFIG
```

Adiciona a configuração do pyenv no `~/.zshrc`.

## Comando 5

```bash
source ~/.zshrc
```

Recarrega o Zsh para o pyenv ficar disponível no terminal atual.

## Comando 6

```bash
pyenv --version
```

Verifica se o pyenv foi instalado corretamente.

## Comando 7

```bash
pyenv install --list | grep " 3\.12"
```

Lista versões disponíveis do Python 3.12.

## Comando 8

```bash
pyenv install 3.12.8
```

Instala o Python 3.12.8 pelo pyenv.

## Comando 9

```bash
pyenv global 3.12.8
```

Define o Python 3.12.8 como versão padrão global do seu usuário.

## Comando 10

```bash
python --version && pip --version
```

Verifica as versões ativas do Python e do pip.

---

# Capítulo 12 — Poetry

Poetry é uma ferramenta para gerenciar dependências e ambientes virtuais de projetos Python.

Ele substitui boa parte do uso manual de `pip`, `venv`, `requirements.txt` e ajuda a organizar o projeto com `pyproject.toml`.

## Comando 1

```bash
curl -sSL https://install.python-poetry.org | python3 -
```

Instala o Poetry usando o instalador oficial.

## Comando 2

```bash
cat <<'POETRY_CONFIG' >> ~/.zshrc

# Poetry
export PATH="$HOME/.local/bin:$PATH"
POETRY_CONFIG
```

Garante que o diretório onde o Poetry é instalado esteja no `PATH` do terminal.

## Comando 3

```bash
source ~/.zshrc
```

Recarrega o Zsh.

## Comando 4

```bash
poetry --version
```

Verifica se o Poetry foi instalado corretamente.

## Comando 5

```bash
poetry config virtualenvs.in-project true
```

Configura o Poetry para criar o ambiente virtual dentro da pasta do projeto, em uma pasta chamada `.venv`. Essa configuração é útil porque deixa o ambiente do projeto mais visível e fácil de usar no VS Code.

---

# Capítulo 13 — Postman ou Insomnia

Postman é uma ferramenta para testar APIs. Com ele você consegue criar requisições HTTP, salvar collections, testar autenticação, headers, body, rotas e ambientes.

## Comando 1

```bash
sudo snap install postman
```

Instala o Postman via Snap.

## Comando 2

```bash
postman
```

Abre o Postman pelo terminal.

## Alternativa: Insomnia

Insomnia também é uma boa ferramenta para testar APIs.

```bash
sudo snap install insomnia
```

Instala o Insomnia via Snap.

---

# Capítulo 14 — Redis CLI

Redis CLI é o cliente de terminal para conversar com servidores Redis.

Mesmo usando Redis via Docker, é útil ter o `redis-cli` instalado no Ubuntu para testar conexão, comandos e filas.

## Comando 1

```bash
sudo apt install -y redis-tools
```

Instala ferramentas de cliente do Redis, incluindo o `redis-cli`.

## Comando 2

```bash
redis-cli --version
```

Verifica se o `redis-cli` foi instalado.

## Comando 3

```bash
redis-cli ping
```

Tenta conectar em um Redis rodando localmente na porta padrão `6379`.

Se houver Redis rodando, a resposta esperada é:

```text
PONG
```

Se não houver Redis rodando, aparecerá erro de conexão. Isso não significa que o `redis-cli` está errado; significa apenas que não existe servidor Redis ativo naquele momento.

---

# Capítulo 15 — PostgreSQL Client

PostgreSQL Client instala o `psql`, que é o cliente de terminal do PostgreSQL.

Mesmo se você roda PostgreSQL via Docker, o `psql` ajuda a testar conexão, executar SQL e depurar banco de dados.

## Comando 1

```bash
sudo apt install -y postgresql-client
```

Instala o cliente PostgreSQL.

## Comando 2

```bash
psql --version
```

Verifica se o `psql` foi instalado.

## Exemplo de conexão

```bash
psql -h localhost -p 5432 -U postgres -d postgres
```

Esse comando tenta conectar em um PostgreSQL local:

- `-h localhost`: host do banco.
- `-p 5432`: porta padrão do PostgreSQL.
- `-U postgres`: usuário.
- `-d postgres`: banco de dados.

---

# Capítulo 16 — NVM, Node.js LTS e npm

NVM é o Node Version Manager. Ele permite instalar e alternar entre versões do Node.js.

Mesmo trabalhando com backend Python, Node.js pode ser necessário para ferramentas de documentação, front-end, CLIs, projetos fullstack e automações.

## Comando 1

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.4/install.sh | bash
```

Instala o NVM usando o script oficial do projeto.

## Comando 2

```bash
source ~/.zshrc
```

Recarrega o Zsh para disponibilizar o comando `nvm`. Se o comando ainda não aparecer, feche e abra o terminal.

## Comando 3

```bash
nvm --version
```

Verifica se o NVM foi instalado corretamente.

## Comando 4

```bash
nvm install --lts
```

Instala a versão LTS mais recente do Node.js.

## Comando 5

```bash
node --version && npm --version
```

Verifica as versões do Node.js e do npm.

---

# Capítulo 17 — GitHub CLI

GitHub CLI, comando `gh`, permite usar recursos do GitHub diretamente pelo terminal.

Você pode autenticar sua conta, clonar repositórios, ver pull requests, issues e interagir com o GitHub sem abrir o navegador o tempo todo.

## Comando 1

```bash
sudo apt update
```

Atualiza a lista de pacotes.

## Comando 2

```bash
sudo apt install -y gh
```

Instala o GitHub CLI pelo repositório disponível no Ubuntu.

## Comando 3

```bash
gh --version
```

Verifica se o GitHub CLI foi instalado.

## Comando 4

```bash
gh auth login
```

Inicia o login da sua conta GitHub pelo terminal.

Fluxo recomendado:

1. Escolha `GitHub.com`.
2. Escolha `HTTPS` ou `SSH`, conforme sua preferência.
3. Autentique pelo navegador quando ele pedir.
4. Confirme a autenticação.

## Comando 5

```bash
gh auth status
```

Verifica se você está autenticado no GitHub CLI.

---

# Capítulo 18 — Checklist final de validação

Depois de instalar tudo, rode este bloco para verificar as principais ferramentas.

## Comando único de verificação

```bash
echo "Sistema:" && lsb_release -a 2>/dev/null || cat /etc/os-release

echo "\nGit:" && git --version

echo "\nZsh:" && zsh --version

echo "\nTmux:" && tmux -V

echo "\nChrome:" && google-chrome --version

echo "\nVS Code:" && code --version

echo "\nDocker:" && docker --version && docker compose version

echo "\nDBeaver:" && snap list dbeaver-ce

echo "\nPython:" && python --version && pip --version

echo "\npyenv:" && pyenv --version

echo "\nPoetry:" && poetry --version

echo "\nRedis CLI:" && redis-cli --version

echo "\nPostgreSQL Client:" && psql --version

echo "\nNode e npm:" && node --version && npm --version

echo "\nGitHub CLI:" && gh --version
```

Esse comando não instala nada. Ele apenas mostra as versões das ferramentas instaladas.

## Lista final esperada

Ao final, seu Ubuntu deve ter:

- Google Chrome
- Visual Studio Code
- DBeaver Community
- Docker Engine
- Docker Compose Plugin
- Git
- Zsh
- Oh My Zsh
- Tmux
- pyenv
- Python gerenciado pelo pyenv
- Poetry
- Postman ou Insomnia
- Redis CLI
- PostgreSQL Client
- NVM
- Node.js LTS
- npm
- GitHub CLI

---

# 99. Referências bibliográficas

- CHROME ENTERPRISE AND EDUCATION HELP. Fazer o download do instalador do Chrome para Linux. Google Support. Disponível em: https://support.google.com/chrome/a/answer/9025926?hl=pt-BR
- VISUAL STUDIO CODE. Installing Visual Studio Code on Linux. Microsoft. Disponível em: https://code.visualstudio.com/docs/setup/linux
- DOCKER DOCS. Install Docker Engine on Ubuntu. Docker. Disponível em: https://docs.docker.com/engine/install/ubuntu/
- DBEAVER DOCUMENTATION. Snap installation. DBeaver. Disponível em: https://dbeaver.com/docs/dbeaver/Snap-installation/
- SNAPCRAFT. DBeaver Community. Snap Store. Disponível em: https://snapcraft.io/dbeaver-ce
- GIT. Install Git on Linux. Git SCM. Disponível em: https://git-scm.com/install/linux
- GIT BOOK. Getting Started — Installing Git. Git SCM. Disponível em: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
- OH MY ZSH. Oh My Zsh GitHub Repository. Disponível em: https://github.com/ohmyzsh/ohmyzsh
- OH MY ZSH WIKI. Installing ZSH. Disponível em: https://github.com/ohmyzsh/ohmyzsh/wiki/Installing-ZSH
- TMUX WIKI. Installing tmux. GitHub. Disponível em: https://github.com/tmux/tmux/wiki/Installing
- PYENV. Simple Python Version Management: pyenv. GitHub. Disponível em: https://github.com/pyenv/pyenv
- PYENV INSTALLER. pyenv-installer. GitHub. Disponível em: https://github.com/pyenv/pyenv-installer
- PYTHON POETRY. Introduction — Poetry Documentation. Disponível em: https://python-poetry.org/docs/
- PYTHON POETRY INSTALLER. Official Poetry installer. Disponível em: https://install.python-poetry.org/
- POSTMAN. Postman API Platform. Disponível em: https://www.postman.com/
- SNAPCRAFT. Install insomnia on Ubuntu. Disponível em: https://snapcraft.io/install/insomnia/ubuntu
- REDIS DOCS. Install Redis on Linux. Disponível em: https://redis.io/docs/latest/operate/oss_and_stack/install/archive/install-redis/install-redis-on-linux/
- REDIS DOCS. Install Redis Open Source on Ubuntu or Debian using APT. Disponível em: https://redis.io/docs/latest/operate/oss_and_stack/install/install-stack/apt/
- POSTGRESQL. PostgreSQL official website. Disponível em: https://www.postgresql.org/
- NVM. Node Version Manager. GitHub. Disponível em: https://github.com/nvm-sh/nvm
- NODE.JS. Download Node.js with nvm. Disponível em: https://nodejs.org/en/download
- GITHUB CLI. GitHub CLI official website. Disponível em: https://cli.github.com/
- GITHUB CLI. GitHub CLI Linux installation documentation. Disponível em: https://github.com/cli/cli/blob/trunk/docs/install_linux.md
