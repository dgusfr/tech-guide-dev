# Docker para Desenvolvimento Backend e Infraestrutura

## Sobre esta apostila

Esta apostila apresenta Docker de forma prática e progressiva, com foco em quem desenvolve aplicações backend e precisa entender como empacotar, executar, testar e integrar serviços em containers. O objetivo não é decorar comandos, mas entender quando usar cada recurso no dia a dia: imagem, container, Dockerfile, volume, rede, registry e Docker Compose.

Docker é uma plataforma para desenvolver, empacotar, distribuir e executar aplicações em containers. Na prática, ele ajuda a reduzir o clássico problema do “na minha máquina funciona”, porque a aplicação passa a rodar junto com suas dependências em um ambiente previsível e reproduzível.

## Como estudar por esta apostila

Leia os capítulos na ordem e execute os comandos em um ambiente de testes. Sempre que um comando criar containers, imagens, redes ou volumes, use os comandos de inspeção (`docker ps`, `docker image ls`, `docker network ls`, `docker volume ls`) para enxergar o que aconteceu. No final, use a cheat sheet como consulta rápida no dia a dia.

> **Importante:** não execute comandos de remoção em massa em máquinas de trabalho sem revisar antes o que será apagado.

## Índice

1. [Capítulo 1 — O problema que o Docker resolve](#capítulo-1--o-problema-que-o-docker-resolve)
2. [Capítulo 2 — Containers, imagens e Docker Engine](#capítulo-2--containers-imagens-e-docker-engine)
3. [Capítulo 3 — Containers vs máquinas virtuais](#capítulo-3--containers-vs-máquinas-virtuais)
4. [Capítulo 4 — Instalação do Docker no Linux](#capítulo-4--instalação-do-docker-no-linux)
5. [Capítulo 5 — Executando seus primeiros containers](#capítulo-5--executando-seus-primeiros-containers)
6. [Capítulo 6 — Imagens, Docker Hub e registries](#capítulo-6--imagens-docker-hub-e-registries)
7. [Capítulo 7 — Dockerfile e construção de imagens](#capítulo-7--dockerfile-e-construção-de-imagens)
8. [Capítulo 8 — Portas e aplicações web em containers](#capítulo-8--portas-e-aplicações-web-em-containers)
9. [Capítulo 9 — Persistência de dados](#capítulo-9--persistência-de-dados)
10. [Capítulo 10 — Redes Docker e comunicação entre containers](#capítulo-10--redes-docker-e-comunicação-entre-containers)
11. [Capítulo 11 — Docker Compose](#capítulo-11--docker-compose)
12. [Capítulo 12 — Exemplo backend com API, banco e cache](#capítulo-12--exemplo-backend-com-api-banco-e-cache)
13. [Capítulo 13 — Debug, inspeção e limpeza](#capítulo-13--debug-inspeção-e-limpeza)
14. [Capítulo 14 — Boas práticas para desenvolvimento e produção](#capítulo-14--boas-práticas-para-desenvolvimento-e-produção)
15. [Capítulo 15 — Erros comuns e como resolver](#capítulo-15--erros-comuns-e-como-resolver)
16. [Capítulo 16 — Exercícios práticos](#capítulo-16--exercícios-práticos)
17. [Cheat sheet — comandos Docker](#cheat-sheet--comandos-docker)
18. [Referências bibliográficas](#referências-bibliográficas)

---

# Capítulo 1 — O problema que o Docker resolve

Antes de falar de comandos, precisamos entender o problema. Em um projeto backend, sua aplicação normalmente depende de uma linguagem específica, bibliotecas, variáveis de ambiente, banco de dados, cache, filas, versões de sistema operacional e configurações de rede. Se cada pessoa do time instala tudo manualmente, é comum aparecerem diferenças entre ambientes.

O Docker resolve esse problema empacotando a aplicação e suas dependências em uma unidade chamada **container**. Em vez de depender da instalação manual de cada máquina, o projeto passa a declarar como deve ser executado.

![Fluxo básico do Docker](./images/docker/fluxo-docker.svg)

Ao final deste capítulo, você será capaz de:

- explicar o problema do “funciona na minha máquina”;
- diferenciar ambiente local, imagem e container;
- entender por que Docker é útil em projetos backend;
- reconhecer quando Docker ajuda e quando ele adiciona complexidade desnecessária.

## 1.1 — O problema

Imagine uma API Python que depende de Python 3.12, FastAPI, PostgreSQL, Redis e algumas variáveis de ambiente. No notebook de um desenvolvedor ela funciona. No notebook de outro, quebra porque a versão do Python é diferente. Em homologação, quebra porque o banco usa outra porta. Em produção, quebra porque faltou uma biblioteca do sistema.

Esse tipo de problema acontece porque o software não é apenas o código. O software também depende do ambiente onde roda.

## 1.2 — O que é Docker?

Docker é uma plataforma que permite criar, distribuir e executar aplicações em containers. O container é um processo isolado que roda com os arquivos, bibliotecas e configurações necessárias para a aplicação funcionar.

Em termos simples: Docker permite transformar a configuração de execução da aplicação em código. Esse código normalmente fica em arquivos como `Dockerfile` e `compose.yaml`.

## 1.3 — Quando usar Docker?

Use Docker quando você precisa padronizar o ambiente de execução, subir dependências locais como banco e cache, testar integração entre serviços ou preparar sua aplicação para rodar em servidores, pipelines de CI/CD e ambientes de nuvem.

Em um backend real, Docker costuma ser útil para subir rapidamente uma stack como:

```text
API Python + PostgreSQL + Redis + RabbitMQ
```

Assim, o time não precisa instalar todos esses serviços diretamente na máquina. Cada serviço roda em um container separado.

## 1.4 — Quando Docker pode ser exagero?

Para scripts muito simples, exercícios pequenos ou programas sem dependências externas, Docker pode adicionar uma camada de complexidade que não é necessária. O ponto é usar Docker quando ele resolve um problema real de ambiente, isolamento, portabilidade ou integração.

## 1.5 — Resumo do capítulo

Docker ajuda a criar ambientes reproduzíveis. Ele não substitui o conhecimento da linguagem, do sistema operacional ou da infraestrutura, mas facilita o empacotamento e a execução da aplicação de forma consistente.

---

# Capítulo 2 — Containers, imagens e Docker Engine

Neste capítulo, vamos organizar os conceitos centrais do Docker. A maior dificuldade inicial é entender a diferença entre imagem e container.

Ao final deste capítulo, você será capaz de:

- explicar o que é uma imagem Docker;
- explicar o que é um container;
- entender o papel do Docker Engine;
- diferenciar Dockerfile, imagem, container e registry.

## 2.1 — O que é uma imagem?

Uma **imagem Docker** é um modelo imutável usado para criar containers. Ela contém o que a aplicação precisa para rodar: sistema de arquivos, dependências, bibliotecas, arquivos do projeto e instruções de inicialização.

Uma boa analogia é pensar na imagem como uma “receita pronta” ou um “molde”. Ela não está em execução. Ela apenas define como um container deve ser criado.

Exemplo:

```bash
docker pull nginx:alpine
```

Esse comando baixa a imagem `nginx:alpine`. Ela ainda não está rodando; ela está apenas disponível localmente.

## 2.2 — O que é um container?

Um **container** é uma instância em execução de uma imagem. Se a imagem é o molde, o container é o objeto criado a partir desse molde.

Exemplo:

```bash
docker run --name meu-nginx -d nginx:alpine
```

Nesse caso, o Docker cria e executa um container chamado `meu-nginx` a partir da imagem `nginx:alpine`.

## 2.3 — O que é Docker Engine?

O Docker Engine é o conjunto de componentes que permite criar e gerenciar imagens, containers, redes e volumes. Na prática, quando você digita comandos como `docker run`, a CLI do Docker conversa com o daemon do Docker, que executa a ação no sistema.

```text
Docker CLI → Docker daemon → containers, imagens, redes e volumes
```

## 2.4 — O que é um registry?

Um **registry** é um local onde imagens são armazenadas e distribuídas. O Docker Hub é o registry público mais conhecido, mas empresas também podem ter registries privados.

Exemplo de fluxo:

```bash
docker build -t minha-api:1.0 .
docker tag minha-api:1.0 usuario/minha-api:1.0
docker push usuario/minha-api:1.0
```

## 2.5 — Tabela de conceitos essenciais

| Conceito | O que é | Exemplo |
|---|---|---|
| Dockerfile | Arquivo com instruções para construir uma imagem | `FROM python:3.12-slim` |
| Imagem | Modelo imutável usado para criar containers | `nginx:alpine` |
| Container | Instância em execução de uma imagem | `meu-nginx` |
| Volume | Área persistente de dados | `postgres_data` |
| Network | Rede virtual entre containers | `backend-net` |
| Registry | Repositório de imagens | Docker Hub |
| Compose | Ferramenta para orquestrar múltiplos containers localmente | `docker compose up` |

## 2.6 — Resumo do capítulo

A imagem define o ambiente. O container executa esse ambiente. O Docker Engine gerencia tudo isso. O registry distribui imagens. O Compose facilita a execução de vários containers relacionados.

---

# Capítulo 3 — Containers vs máquinas virtuais

Containers e máquinas virtuais resolvem problemas parecidos: isolar aplicações. A diferença está no nível de isolamento e no consumo de recursos.

![Máquinas virtuais vs containers](./images/docker/vm-vs-container.svg)

Ao final deste capítulo, você será capaz de:

- explicar o papel do kernel;
- diferenciar virtualização tradicional e containerização;
- entender por que containers são mais leves;
- reconhecer limitações de segurança de containers.

## 3.1 — O que é kernel?

O kernel é o núcleo do sistema operacional. Ele gerencia CPU, memória, disco, processos, rede e comunicação com o hardware. Todo programa que roda no sistema depende do kernel para acessar recursos da máquina.

## 3.2 — Máquinas virtuais

Uma máquina virtual simula uma máquina completa. Ela possui seu próprio sistema operacional e seu próprio kernel. Isso oferece isolamento forte, mas também consome mais recursos, porque cada VM carrega um sistema operacional inteiro.

VMs são úteis quando você precisa rodar sistemas operacionais diferentes, isolar ambientes com alta separação ou manter aplicações legadas que dependem de um sistema específico.

## 3.3 — Containers

Containers compartilham o kernel do host. Eles não precisam carregar um sistema operacional completo. Por isso, costumam iniciar mais rápido e consumir menos recursos do que VMs.

O isolamento dos containers é feito principalmente com recursos do Linux, como **namespaces** e **cgroups**.

## 3.4 — Namespaces

Namespaces criam “bolhas” de isolamento. Eles permitem que um container tenha sua própria visão de processos, rede, sistema de arquivos e hostname.

Exemplos:

| Namespace | Isola |
|---|---|
| PID | Processos |
| NET | Rede e interfaces |
| MNT | Montagens e sistema de arquivos |
| IPC | Comunicação entre processos |
| UTS | Hostname |

## 3.5 — cgroups

Cgroups controlam recursos. Com eles, o Docker consegue limitar quanto de CPU, memória e I/O um container pode usar.

Exemplo:

```bash
docker run -d --name app-limitada --cpus="1.0" --memory="512m" nginx:alpine
```

Esse container fica limitado a aproximadamente 1 CPU e 512 MB de memória.

## 3.6 — Resumo do capítulo

VMs virtualizam uma máquina inteira. Containers isolam processos compartilhando o kernel do host. Por isso, containers são leves e rápidos, mas exigem cuidado com segurança, permissões e isolamento.

---

# Capítulo 4 — Instalação do Docker no Linux

Este capítulo usa Ubuntu/Debian como referência, porque é um ambiente comum para desenvolvimento backend. Em outras distribuições, consulte a documentação oficial do Docker.

Ao final deste capítulo, você será capaz de:

- instalar Docker Engine no Linux;
- validar a instalação;
- configurar o uso sem `sudo` com cuidado;
- entender o risco do grupo `docker`.

## 4.1 — Atualizar pacotes

```bash
sudo apt update
sudo apt upgrade -y
```

## 4.2 — Instalar dependências

```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
```

## 4.3 — Adicionar chave GPG e repositório oficial

```bash
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

```bash
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## 4.4 — Instalar Docker Engine e Compose plugin

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

## 4.5 — Testar instalação

```bash
sudo docker run hello-world
```

Se a mensagem de teste aparecer, o Docker está funcionando.

## 4.6 — Usar Docker sem sudo

```bash
sudo usermod -aG docker $USER
newgrp docker
```

Depois teste:

```bash
docker ps
```

> **Atenção:** o grupo `docker` concede privilégios equivalentes a root sobre o host. Em máquina pessoal isso costuma ser aceitável para desenvolvimento, mas em servidores deve ser avaliado com cuidado. Para cenários mais restritos, considere Docker Rootless.

## 4.7 — Resumo do capítulo

A instalação correta envolve usar o repositório oficial, instalar o Docker Engine, o Buildx e o Compose plugin. Depois, valide com `hello-world` e configure o uso sem `sudo` apenas se fizer sentido para seu ambiente.

---

# Capítulo 5 — Executando seus primeiros containers

Agora vamos praticar os comandos essenciais. O objetivo é entender o ciclo de vida de um container: criar, executar, listar, acessar, parar e remover.

Ao final deste capítulo, você será capaz de:

- executar containers com `docker run`;
- listar containers ativos e parados;
- acessar o terminal de um container;
- parar, iniciar e remover containers.

## 5.1 — Primeiro teste com hello-world

```bash
docker run hello-world
```

O Docker procura a imagem localmente. Se não encontrar, baixa do registry padrão. Depois cria um container, executa o processo principal e finaliza.

## 5.2 — Rodando Ubuntu interativo

```bash
docker run -it ubuntu bash
```

O que cada parte faz:

| Parte | Significado |
|---|---|
| `docker run` | cria e executa um container |
| `-it` | modo interativo com terminal |
| `ubuntu` | imagem usada |
| `bash` | comando executado dentro do container |

Dentro do container, teste:

```bash
cat /etc/os-release
pwd
ls
```

Saia com:

```bash
exit
```

## 5.3 — Por que alguns containers param imediatamente?

Um container vive enquanto o processo principal dele está em execução. Se você executa `docker run ubuntu`, o container pode parar logo em seguida porque não há um processo contínuo mantendo-o vivo.

Para manter o container rodando por um tempo:

```bash
docker run -d --name meu-ubuntu ubuntu sleep 1d
```

## 5.4 — Listar containers

```bash
docker ps
```

Lista apenas containers em execução.

```bash
docker ps -a
```

Lista containers em execução e containers parados.

## 5.5 — Acessar um container em execução

```bash
docker exec -it meu-ubuntu bash
```

Use `exec` quando o container já está rodando e você quer executar um comando dentro dele.

## 5.6 — Ver logs

```bash
docker logs meu-ubuntu
```

Para acompanhar logs em tempo real:

```bash
docker logs -f meu-ubuntu
```

## 5.7 — Parar, iniciar e remover

```bash
docker stop meu-ubuntu
docker start meu-ubuntu
docker rm meu-ubuntu
```

Se o container estiver em execução e você quiser remover à força:

```bash
docker rm -f meu-ubuntu
```

## 5.8 — Usando `--rm`

```bash
docker run --rm ubuntu echo "container temporário"
```

A opção `--rm` remove o container automaticamente quando ele termina. Use para testes rápidos.

## 5.9 — O que pode dar errado?

Se você tentar remover um container em execução, o Docker mostrará erro. Pare primeiro com `docker stop` ou use `docker rm -f` se tiver certeza.

Se o shell `bash` não existir na imagem, use `sh`:

```bash
docker exec -it nome-do-container sh
```

## 5.10 — Resumo do capítulo

O ciclo básico é: `run` para criar e executar, `ps` para listar, `exec` para entrar, `logs` para diagnosticar, `stop` para parar e `rm` para remover.

---

# Capítulo 6 — Imagens, Docker Hub e registries

Imagens são a base do Docker. Para trabalhar bem com Docker, você precisa saber baixar, listar, nomear, versionar, remover e publicar imagens.

Ao final deste capítulo, você será capaz de:

- baixar imagens com `pull`;
- listar imagens locais;
- entender tags;
- publicar imagens em um registry.

## 6.1 — Baixar uma imagem

```bash
docker pull nginx:alpine
```

A parte antes dos dois pontos é o nome da imagem. A parte depois dos dois pontos é a tag. A tag normalmente indica versão ou variação da imagem.

```text
nginx:alpine
│     └── tag
└── imagem
```

## 6.2 — Por que evitar `latest` em produção?

A tag `latest` não significa necessariamente “mais estável”. Ela é apenas uma tag padrão. Em produção, prefira versões explícitas, como:

```bash
postgres:16
redis:7-alpine
python:3.12-slim
```

Isso deixa o ambiente mais previsível.

## 6.3 — Listar imagens locais

```bash
docker image ls
```

## 6.4 — Remover uma imagem

```bash
docker image rm nginx:alpine
```

Se algum container depende dessa imagem, remova o container primeiro.

## 6.5 — Nomear e publicar uma imagem

```bash
docker build -t minha-api:1.0 .
docker tag minha-api:1.0 meuusuario/minha-api:1.0
docker login
docker push meuusuario/minha-api:1.0
```

## 6.6 — Quando usar registry privado?

Use registry privado quando a imagem contém código da empresa, configurações internas ou software que não deve ficar público. Em empresas, é comum usar GitHub Container Registry, GitLab Container Registry, AWS ECR, Azure Container Registry ou Google Artifact Registry.

## 6.7 — Resumo do capítulo

Imagens devem ser versionadas com cuidado. Use tags explícitas, publique apenas o que pode ser compartilhado e mantenha imagens privadas em registries protegidos.

---

# Capítulo 7 — Dockerfile e construção de imagens

O Dockerfile é o arquivo que descreve como construir uma imagem. Ele transforma conhecimento de ambiente em instruções versionáveis dentro do projeto.

![Camadas de uma imagem Docker](./images/docker/camadas-imagem.svg)

Ao final deste capítulo, você será capaz de:

- entender as instruções principais de um Dockerfile;
- construir imagens com `docker build`;
- usar cache de camadas de forma inteligente;
- criar Dockerfiles mais seguros e eficientes.

## 7.1 — Dockerfile mínimo para aplicação Python

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "main.py"]
```

Esse Dockerfile usa uma imagem base do Python, define `/app` como diretório de trabalho, copia as dependências, instala os pacotes, copia o restante do projeto e define o comando padrão.

## 7.2 — Explicando as instruções principais

| Instrução | Para que serve | Exemplo |
|---|---|---|
| `FROM` | define a imagem base | `FROM python:3.12-slim` |
| `WORKDIR` | define o diretório de trabalho | `WORKDIR /app` |
| `COPY` | copia arquivos do host para a imagem | `COPY . .` |
| `RUN` | executa comandos durante o build | `RUN pip install ...` |
| `CMD` | comando padrão ao iniciar o container | `CMD ["python", "main.py"]` |
| `ENTRYPOINT` | comando principal fixo | `ENTRYPOINT ["python"]` |
| `EXPOSE` | documenta a porta usada pelo container | `EXPOSE 8000` |
| `ENV` | define variável de ambiente | `ENV PYTHONUNBUFFERED=1` |
| `ARG` | define variável usada no build | `ARG APP_VERSION=dev` |
| `USER` | define usuário de execução | `USER appuser` |

## 7.3 — Build da imagem

```bash
docker build -t minha-api:1.0 .
```

O ponto final (`.`) indica o **contexto de build**, ou seja, a pasta enviada para o Docker construir a imagem.

## 7.4 — Build context e `.dockerignore`

O contexto de build pode ficar enorme se você enviar arquivos desnecessários, como `.git`, ambiente virtual, caches e logs. Para evitar isso, crie um `.dockerignore`.

Exemplo:

```gitignore
.git
.venv
__pycache__/
.pytest_cache/
.env
*.log
node_modules/
dist/
build/
```

Isso torna o build mais rápido, menor e mais seguro.

## 7.5 — Cache de camadas

Cada instrução do Dockerfile gera uma camada. Se uma camada não mudou, o Docker pode reutilizá-la. Por isso, é comum copiar primeiro o arquivo de dependências e instalar pacotes antes de copiar o restante do código.

Boa ordem:

```dockerfile
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY . .
```

Má ordem:

```dockerfile
COPY . .
RUN pip install --no-cache-dir -r requirements.txt
```

Na má ordem, qualquer alteração no código invalida a camada de instalação de dependências.

## 7.6 — `CMD` vs `ENTRYPOINT`

Use `CMD` quando você quer definir o comando padrão, mas permitir substituição fácil.

```dockerfile
CMD ["python", "main.py"]
```

Use `ENTRYPOINT` quando a imagem representa um executável fixo.

```dockerfile
ENTRYPOINT ["python"]
CMD ["main.py"]
```

Nesse caso, o `ENTRYPOINT` é o comando base e o `CMD` fornece argumentos padrão.

## 7.7 — Dockerfile para FastAPI

```dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app/ ./app/

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

Esse exemplo é comum em backend Python. O container escuta em `0.0.0.0` para permitir acesso externo via mapeamento de portas.

## 7.8 — Build e execução da API

```bash
docker build -t minha-fastapi:1.0 .
docker run --rm -p 8000:8000 minha-fastapi:1.0
```

Acesse:

```text
http://localhost:8000
```

## 7.9 — Multi-stage build

Multi-stage build permite usar uma imagem maior para build e copiar apenas o resultado final para uma imagem menor.

Exemplo conceitual para aplicação Node:

```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
```

O resultado final não carrega todas as ferramentas de build do Node, apenas os arquivos estáticos.

## 7.10 — Resumo do capítulo

Dockerfile bem escrito melhora build, segurança e manutenção. Copie dependências antes do código, use `.dockerignore`, evite pacotes desnecessários, prefira tags explícitas e pense no cache de camadas.

---

# Capítulo 8 — Portas e aplicações web em containers

Containers têm rede isolada. Se uma API roda dentro do container na porta 8000, isso não significa que ela esteja acessível automaticamente no seu navegador. Para acessar a aplicação do host, você precisa mapear portas.

Ao final deste capítulo, você será capaz de:

- entender a diferença entre porta do host e porta do container;
- mapear portas com `-p`;
- executar aplicações web em containers;
- diagnosticar erro de conexão.

## 8.1 — Mapeamento manual de portas

```bash
docker run -d --name web -p 8080:80 nginx:alpine
```

Formato:

```text
-p PORTA_DO_HOST:PORTA_DO_CONTAINER
```

Nesse exemplo, a porta 8080 da sua máquina aponta para a porta 80 do container.

Acesse:

```text
http://localhost:8080
```

## 8.2 — Mapeamento automático

```bash
docker run -d -P nginx:alpine
```

O `-P` mapeia automaticamente portas expostas pela imagem para portas aleatórias do host. Para descobrir a porta:

```bash
docker ps
```

## 8.3 — Erro comum: aplicação escutando em `localhost`

Dentro do container, `localhost` significa o próprio container, não sua máquina. Em APIs Python, Node ou Java, prefira escutar em `0.0.0.0` dentro do container.

Exemplo FastAPI:

```bash
uvicorn app.main:app --host 0.0.0.0 --port 8000
```

## 8.4 — Verificar se a porta está publicada

```bash
docker port web
```

Ou:

```bash
docker ps
```

## 8.5 — Resumo do capítulo

A aplicação precisa escutar dentro do container e a porta precisa ser publicada no host. Se não conseguir acessar, verifique se usou `-p`, se a aplicação está em `0.0.0.0` e se a porta do host não está ocupada.

---

# Capítulo 9 — Persistência de dados

Por padrão, alterações feitas dentro da camada gravável de um container são perdidas quando o container é removido. Para banco de dados, uploads, arquivos gerados e dados importantes, é preciso usar persistência.

![Persistência de dados no Docker](./images/docker/persistencia-dados.svg)

Ao final deste capítulo, você será capaz de:

- explicar a camada gravável do container;
- usar bind mounts;
- usar volumes;
- usar tmpfs;
- escolher o tipo de persistência correto.

## 9.1 — O problema da camada gravável

Quando um container é iniciado, o Docker adiciona uma camada gravável sobre as camadas imutáveis da imagem. Tudo que é criado ali pertence ao container. Se o container for removido, esses dados podem desaparecer.

Por isso, um banco como PostgreSQL não deve depender apenas da camada interna do container.

## 9.2 — Bind mount

Bind mount liga um caminho da sua máquina a um caminho dentro do container.

```bash
mkdir -p ~/docker-lab/app

docker run --rm -it \
  --mount type=bind,source=$HOME/docker-lab/app,target=/app \
  ubuntu bash
```

Dentro do container:

```bash
cd /app
touch arquivo.txt
```

No host, o arquivo aparecerá em `~/docker-lab/app`.

## 9.3 — Quando usar bind mount?

Use bind mount durante desenvolvimento, quando você quer editar arquivos no host e ver a alteração dentro do container.

Exemplo comum:

```bash
docker run --rm -it \
  -v "$PWD":/app \
  -w /app \
  python:3.12-slim python main.py
```

Aqui, a pasta atual do host é montada em `/app` no container.

## 9.4 — Volume

Volume é uma área de dados gerenciada pelo Docker. É a opção mais recomendada para persistir dados de containers, especialmente bancos.

Criar volume:

```bash
docker volume create postgres_data
```

Usar volume:

```bash
docker run -d \
  --name postgres \
  -e POSTGRES_PASSWORD=postgres \
  -v postgres_data:/var/lib/postgresql/data \
  postgres:16
```

Listar volumes:

```bash
docker volume ls
```

Inspecionar:

```bash
docker volume inspect postgres_data
```

## 9.5 — tmpfs

tmpfs armazena dados temporários em memória. Os dados não são gravados em disco e somem quando o container para.

```bash
docker run --rm -it --tmpfs /tmp/sensivel ubuntu bash
```

Use tmpfs para arquivos temporários sensíveis, caches transitórios ou dados que não devem persistir.

## 9.6 — Comparação

| Tipo | Quem gerencia? | Persiste? | Melhor uso |
|---|---|---|---|
| Bind mount | Host | Sim | Desenvolvimento local e código fonte |
| Volume | Docker | Sim | Bancos de dados e dados importantes |
| tmpfs | Memória | Não | Dados temporários sensíveis |

## 9.7 — O que pode dar errado?

Um bind mount pode sobrescrever arquivos internos do container. Se você montar uma pasta vazia sobre uma pasta que tinha arquivos da imagem, os arquivos internos ficarão ocultos enquanto o mount existir.

Exemplo perigoso:

```bash
docker run -v "$PWD":/usr/share/nginx/html nginx:alpine
```

Se a pasta atual estiver vazia, o Nginx não verá o conteúdo original da imagem em `/usr/share/nginx/html`.

## 9.8 — Resumo do capítulo

Use volumes para persistência robusta, bind mounts para desenvolvimento e tmpfs para dados temporários. Nunca confie em dados gravados apenas dentro do container se eles precisam sobreviver à remoção.

---

# Capítulo 10 — Redes Docker e comunicação entre containers

Aplicações reais raramente rodam em um container só. Uma API precisa conversar com banco, cache, filas e outros serviços. Para isso, o Docker fornece redes virtuais.

![Rede bridge personalizada e DNS interno](./images/docker/rede-docker.svg)

Ao final deste capítulo, você será capaz de:

- listar redes Docker;
- entender a rede `bridge`;
- criar redes definidas pelo usuário;
- conectar containers pelo nome;
- entender os modos `host` e `none`.

## 10.1 — Listar redes

```bash
docker network ls
```

Redes comuns:

| Rede | Uso |
|---|---|
| `bridge` | rede padrão para containers no mesmo host |
| `host` | container usa a rede do host |
| `none` | container fica sem rede |

## 10.2 — Criar uma rede bridge personalizada

```bash
docker network create backend-net
```

Contêineres na mesma rede definida pelo usuário podem se comunicar por nome.

## 10.3 — Comunicação por nome

Crie uma rede:

```bash
docker network create minha-bridge
```

Suba um container chamado `api`:

```bash
docker run -d --name api --network minha-bridge nginx:alpine
```

Suba outro container para testar:

```bash
docker run --rm -it --network minha-bridge alpine sh
```

Dentro do Alpine:

```bash
wget -qO- http://api
```

O nome `api` é resolvido automaticamente pelo DNS interno da rede definida pelo usuário.

## 10.4 — Por que evitar IP fixo de container?

O IP interno do container pode mudar quando ele é recriado. Em vez de configurar uma API para acessar `172.18.0.3`, configure para acessar o nome do serviço, como `postgres`, `redis` ou `api`.

## 10.5 — Rede `host`

```bash
docker run --network host nginx:alpine
```

Nesse modo, o container compartilha a rede do host. Não é preciso `-p`, mas o isolamento de rede é reduzido e podem ocorrer conflitos de porta. Use apenas quando houver uma razão clara.

## 10.6 — Rede `none`

```bash
docker run -d --name sem-rede --network none ubuntu sleep 1d
```

O container não terá acesso à rede. Pode ser útil para processamento offline ou testes de isolamento.

## 10.7 — Banco e API na mesma rede

```bash
docker network create backend-net

docker run -d \
  --name db \
  --network backend-net \
  -e POSTGRES_PASSWORD=postgres \
  postgres:16

docker run -d \
  --name api \
  --network backend-net \
  -p 8000:8000 \
  minha-api:1.0
```

A API deve se conectar ao banco usando `db` como hostname.

Exemplo de variável:

```bash
DATABASE_URL=postgresql://postgres:postgres@db:5432/postgres
```

## 10.8 — Resumo do capítulo

Para comunicação entre containers, prefira redes definidas pelo usuário e nomes de serviço. Use `-p` apenas para expor serviços ao host. Containers internos podem se comunicar diretamente pela rede Docker sem publicar portas.

---

# Capítulo 11 — Docker Compose

Docker Compose permite declarar uma aplicação multi-container em um arquivo YAML. Em vez de executar vários `docker run` manualmente, você define serviços, redes e volumes em `compose.yaml`.

![Docker Compose: aplicação multi-container](./images/docker/compose-stack.svg)

Ao final deste capítulo, você será capaz de:

- entender a função do Compose;
- criar um `compose.yaml`;
- subir e derrubar uma stack local;
- usar volumes, redes e variáveis de ambiente.

## 11.1 — Por que usar Compose?

Imagine subir manualmente uma API, PostgreSQL, Redis e RabbitMQ. Você precisa lembrar nomes, redes, portas, volumes e variáveis. O Compose coloca tudo isso em um arquivo versionado.

## 11.2 — Exemplo mínimo

```yaml
services:
  web:
    image: nginx:alpine
    ports:
      - "8080:80"
```

Subir:

```bash
docker compose up -d
```

Parar e remover containers da stack:

```bash
docker compose down
```

## 11.3 — Compose com API e Postgres

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://postgres:postgres@db:5432/app
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: app
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

Subir:

```bash
docker compose up -d --build
```

Ver logs:

```bash
docker compose logs -f api
```

Entrar no container da API:

```bash
docker compose exec api sh
```

## 11.4 — `depends_on` não garante aplicação pronta

`depends_on` controla a ordem de inicialização dos containers, mas não garante que o banco já esteja pronto para aceitar conexões. Para isso, use healthchecks ou lógica de retry na aplicação.

Exemplo de healthcheck para PostgreSQL:

```yaml
services:
  db:
    image: postgres:16
    environment:
      POSTGRES_PASSWORD: postgres
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 3s
      retries: 5
```

## 11.5 — Networks no Compose

Por padrão, o Compose cria uma rede para o projeto. Os serviços se encontram pelo nome.

```text
api → db:5432
api → redis:6379
```

Você também pode declarar redes explicitamente:

```yaml
services:
  api:
    build: .
    networks:
      - backend
  db:
    image: postgres:16
    networks:
      - backend

networks:
  backend:
```

## 11.6 — Volumes no Compose

```yaml
services:
  db:
    image: postgres:16
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

O volume persiste os dados do banco mesmo se o container for removido por `docker compose down`.

## 11.7 — Variáveis de ambiente

Você pode usar um arquivo `.env` para variáveis do Compose:

```env
POSTGRES_PASSWORD=postgres
APP_PORT=8000
```

E referenciar no YAML:

```yaml
services:
  api:
    ports:
      - "${APP_PORT}:8000"
```

> **Atenção:** não faça commit de `.env` com senhas reais.

## 11.8 — Resumo do capítulo

Docker Compose é essencial para ambiente local de backend. Ele facilita subir dependências, padronizar comandos, documentar infraestrutura local e simular parte da arquitetura de produção.

---

# Capítulo 12 — Exemplo backend com API, banco e cache

Neste capítulo, vamos montar um cenário próximo do dia a dia: uma API backend que usa PostgreSQL e Redis.

Ao final deste capítulo, você será capaz de:

- montar uma stack local com Compose;
- entender comunicação entre serviços;
- persistir banco em volume;
- expor apenas a API para o host.

## 12.1 — Estrutura do projeto

```text
meu-backend/
├── app/
│   └── main.py
├── Dockerfile
├── requirements.txt
├── compose.yaml
└── .dockerignore
```

## 12.2 — `requirements.txt`

```txt
fastapi
uvicorn[standard]
psycopg[binary]
redis
```

## 12.3 — Dockerfile

```dockerfile
FROM python:3.12-slim

ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY app/ ./app/

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## 12.4 — `compose.yaml`

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://postgres:postgres@db:5432/app
      REDIS_URL: redis://redis:6379/0
    depends_on:
      - db
      - redis

  db:
    image: postgres:16
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: app
    volumes:
      - postgres_data:/var/lib/postgresql/data

  redis:
    image: redis:7-alpine

volumes:
  postgres_data:
```

## 12.5 — O que acontece nesse Compose?

A API é construída a partir do Dockerfile local. O banco usa a imagem oficial `postgres:16`. O Redis usa `redis:7-alpine`. A API acessa o banco pelo hostname `db` e o Redis pelo hostname `redis`, porque o Compose coloca todos os serviços na mesma rede padrão do projeto.

Apenas a API publica porta para o host:

```yaml
ports:
  - "8000:8000"
```

O banco e o Redis ficam acessíveis internamente pela rede do Compose, mas não são expostos diretamente para sua máquina.

## 12.6 — Subindo o ambiente

```bash
docker compose up -d --build
```

Ver containers:

```bash
docker compose ps
```

Ver logs:

```bash
docker compose logs -f
```

Derrubar:

```bash
docker compose down
```

Derrubar removendo volumes:

```bash
docker compose down -v
```

> Use `-v` com cuidado, porque remove os dados persistidos em volumes da stack.

## 12.7 — Resumo do capítulo

Esse padrão é muito comum para backend: API exposta para o host, banco e cache internos na rede Docker, volume para dados persistentes e Compose para gerenciar tudo.

---

# Capítulo 13 — Debug, inspeção e limpeza

Saber subir containers é apenas metade do trabalho. No dia a dia, você precisa diagnosticar problemas: container reiniciando, porta ocupada, variável errada, volume incorreto ou imagem desatualizada.

Ao final deste capítulo, você será capaz de:

- inspecionar containers, imagens, redes e volumes;
- ver consumo de recursos;
- copiar arquivos;
- limpar recursos com segurança.

## 13.1 — Inspecionar container

```bash
docker inspect nome-do-container
```

Para filtrar o IP:

```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' nome-do-container
```

## 13.2 — Ver processos internos

```bash
docker top nome-do-container
```

## 13.3 — Ver consumo de recursos

```bash
docker stats
```

## 13.4 — Copiar arquivos entre host e container

Do container para o host:

```bash
docker cp api:/app/log.txt ./log.txt
```

Do host para o container:

```bash
docker cp ./arquivo.txt api:/app/arquivo.txt
```

## 13.5 — Ver alterações no filesystem do container

```bash
docker diff nome-do-container
```

Isso mostra arquivos criados, alterados ou removidos na camada gravável.

## 13.6 — Uso de disco

```bash
docker system df
```

## 13.7 — Limpeza segura

Remover containers parados:

```bash
docker container prune
```

Remover imagens não utilizadas:

```bash
docker image prune
```

Remover redes não utilizadas:

```bash
docker network prune
```

Remover volumes não utilizados:

```bash
docker volume prune
```

Remover vários recursos não usados:

```bash
docker system prune
```

Remover também imagens não usadas por containers:

```bash
docker system prune -a
```

> **Cuidado:** `prune` remove recursos não utilizados. Antes de executar em máquina de trabalho, revise se há volumes ou imagens importantes.

## 13.8 — Resumo do capítulo

Diagnosticar Docker exige olhar logs, inspect, redes, volumes e recursos. Antes de apagar, entenda o que será removido.

---

# Capítulo 14 — Boas práticas para desenvolvimento e produção

Docker funciona mesmo com imagens mal construídas, mas isso não significa que estejam boas. Imagens grandes, inseguras ou com arquivos sensíveis podem causar problemas reais.

Ao final deste capítulo, você será capaz de:

- criar imagens menores e mais previsíveis;
- evitar vazamento de arquivos sensíveis;
- usar usuários não root;
- separar ambiente de desenvolvimento e produção.

## 14.1 — Use `.dockerignore`

Inclua arquivos que não devem ir para a imagem:

```gitignore
.git
.env
.venv
node_modules/
__pycache__/
.pytest_cache/
coverage/
*.log
```

## 14.2 — Não copie segredos para a imagem

Nunca coloque senhas, tokens, chaves SSH ou arquivos `.env` reais dentro da imagem. Imagem publicada em registry pode ser copiada por outras pessoas ou ficar armazenada em cache.

## 14.3 — Use tags explícitas

Prefira:

```dockerfile
FROM python:3.12-slim
```

Evite em produção:

```dockerfile
FROM python:latest
```

## 14.4 — Instale apenas o necessário

Quanto mais pacotes na imagem, maior a superfície de ataque e maior o tamanho da imagem.

## 14.5 — Rode como usuário não root

Exemplo:

```dockerfile
FROM python:3.12-slim

RUN useradd -m appuser
WORKDIR /app
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
COPY app/ ./app/

USER appuser

CMD ["python", "-m", "app.main"]
```

## 14.6 — Um container não deve ser uma VM

Evite colocar banco, API, cache, cron e fila no mesmo container. Prefira separar responsabilidades. Uma API em um container, banco em outro, cache em outro.

## 14.7 — Configure logs para stdout/stderr

Aplicações em containers devem escrever logs no stdout/stderr. Assim, você consegue ler com:

```bash
docker logs nome-do-container
```

## 14.8 — Use healthcheck quando necessário

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:8000/health || exit 1
```

Healthcheck ajuda orquestradores e ferramentas a detectar se a aplicação está saudável.

## 14.9 — Separe desenvolvimento e produção

Em desenvolvimento, bind mount e hot reload são úteis. Em produção, prefira imagem fechada, sem montar código do host.

Desenvolvimento:

```yaml
services:
  api:
    volumes:
      - .:/app
```

Produção:

```yaml
services:
  api:
    image: registry/minha-api:1.0.0
```

## 14.10 — Resumo do capítulo

Boas práticas reduzem tamanho, aumentam segurança e tornam o ambiente previsível. Para backend, o mais importante é controlar dependências, segredos, usuários, logs, volumes e redes.

---

# Capítulo 15 — Erros comuns e como resolver

Este capítulo reúne problemas comuns de quem começa a usar Docker no desenvolvimento backend.

## 15.1 — “Port is already allocated”

Erro típico:

```text
Bind for 0.0.0.0:8000 failed: port is already allocated
```

Significa que a porta do host já está em uso.

Verifique containers:

```bash
docker ps
```

Use outra porta:

```bash
docker run -p 8001:8000 minha-api:1.0
```

## 15.2 — API não acessa o banco

Se estiver usando Compose, não use `localhost` para acessar outro container. Use o nome do serviço.

Errado:

```env
DATABASE_URL=postgresql://postgres:postgres@localhost:5432/app
```

Correto:

```env
DATABASE_URL=postgresql://postgres:postgres@db:5432/app
```

## 15.3 — Container sai imediatamente

O processo principal terminou. Veja logs:

```bash
docker logs nome-do-container
```

Se quiser manter um container de teste vivo:

```bash
docker run -d --name teste ubuntu sleep 1d
```

## 15.4 — Alterei o Dockerfile, mas nada mudou

Reconstrua a imagem:

```bash
docker compose up -d --build
```

Ou sem cache:

```bash
docker build --no-cache -t minha-api:1.0 .
```

## 15.5 — Volume manteve dados antigos

Se você recriou o banco mas os dados continuam iguais, provavelmente o volume foi preservado.

Remover stack com volumes:

```bash
docker compose down -v
```

Ou remover volume específico:

```bash
docker volume rm nome-do-volume
```

## 15.6 — Permissão negada ao usar Docker sem sudo

Adicione o usuário ao grupo `docker` e abra uma nova sessão:

```bash
sudo usermod -aG docker $USER
newgrp docker
```

## 15.7 — Bind mount com caminho errado

Use caminho absoluto ou `$PWD`:

```bash
docker run --rm -v "$PWD":/app -w /app python:3.12-slim python main.py
```

## 15.8 — Resumo do capítulo

A maioria dos problemas iniciais vem de porta ocupada, hostname errado, volume antigo, build cache ou processo principal encerrando. Logs e inspect são seus melhores amigos.

---

# Capítulo 16 — Exercícios práticos

## 16.1 — Exercício 1: primeiro container

Execute um container Ubuntu, entre nele, crie um arquivo e depois remova o container.

Objetivo: entender que arquivos criados dentro do container somem quando ele é removido.

## 16.2 — Exercício 2: Nginx com porta publicada

Suba um Nginx na porta 8080 do host.

```bash
docker run -d --name nginx-lab -p 8080:80 nginx:alpine
```

Acesse `http://localhost:8080` e depois remova o container.

## 16.3 — Exercício 3: bind mount

Crie um `index.html` no host e monte no Nginx.

```bash
mkdir -p site
printf '<h1>Docker funcionando</h1>' > site/index.html

docker run -d --name site \
  -p 8080:80 \
  -v "$PWD/site":/usr/share/nginx/html:ro \
  nginx:alpine
```

Objetivo: alterar o arquivo no host e ver a alteração no navegador.

## 16.4 — Exercício 4: volume com PostgreSQL

Suba um PostgreSQL com volume nomeado.

```bash
docker volume create pg_lab

docker run -d --name pg-lab \
  -e POSTGRES_PASSWORD=postgres \
  -v pg_lab:/var/lib/postgresql/data \
  postgres:16
```

Pare e remova o container. Depois suba outro usando o mesmo volume.

## 16.5 — Exercício 5: rede personalizada

Crie dois containers na mesma rede e faça um acessar o outro pelo nome.

```bash
docker network create lab-net

docker run -d --name web --network lab-net nginx:alpine

docker run --rm -it --network lab-net alpine sh
```

Dentro do Alpine:

```sh
wget -qO- http://web
```

## 16.6 — Exercício 6: Compose com API e banco

Crie um `compose.yaml` com uma API e um PostgreSQL. A API deve usar o hostname `db` para se conectar ao banco.

## 16.7 — Fixando o conhecimento

1. Qual é a diferença entre imagem e container?
2. Por que `localhost` dentro de um container não aponta para outro container?
3. Quando usar volume em vez de bind mount?
4. Por que `docker compose down -v` pode ser perigoso?
5. Qual é a vantagem de usar `.dockerignore`?
6. Por que uma API dentro do container deve escutar em `0.0.0.0`?

---

# Cheat sheet — comandos Docker

## Instalação e validação

| Quando usar | Comando |
|---|---|
| Testar se o Docker funciona | `docker run hello-world` |
| Ver versão do Docker | `docker version` |
| Ver informações gerais | `docker info` |
| Rodar Docker sem sudo no Linux | `sudo usermod -aG docker $USER` |
| Aplicar grupo na sessão atual | `newgrp docker` |

## Containers

| Quando usar | Comando |
|---|---|
| Rodar container simples | `docker run nginx:alpine` |
| Rodar em segundo plano | `docker run -d nginx:alpine` |
| Dar nome ao container | `docker run --name web nginx:alpine` |
| Rodar interativo | `docker run -it ubuntu bash` |
| Remover ao finalizar | `docker run --rm ubuntu echo teste` |
| Mapear porta | `docker run -p 8080:80 nginx:alpine` |
| Definir variável de ambiente | `docker run -e APP_ENV=dev minha-api` |
| Limitar memória | `docker run --memory=512m minha-api` |
| Limitar CPU | `docker run --cpus="1.0" minha-api` |
| Listar containers ativos | `docker ps` |
| Listar todos os containers | `docker ps -a` |
| Parar container | `docker stop nome` |
| Iniciar container parado | `docker start nome` |
| Reiniciar container | `docker restart nome` |
| Remover container parado | `docker rm nome` |
| Remover container rodando | `docker rm -f nome` |
| Ver logs | `docker logs nome` |
| Acompanhar logs | `docker logs -f nome` |
| Entrar no container | `docker exec -it nome bash` |
| Entrar se não tiver bash | `docker exec -it nome sh` |
| Ver processos | `docker top nome` |
| Ver uso de recursos | `docker stats` |
| Copiar do container para host | `docker cp nome:/app/file.txt ./file.txt` |
| Copiar do host para container | `docker cp ./file.txt nome:/app/file.txt` |
| Ver alterações no filesystem | `docker diff nome` |

## Imagens

| Quando usar | Comando |
|---|---|
| Baixar imagem | `docker pull nginx:alpine` |
| Listar imagens | `docker image ls` |
| Construir imagem | `docker build -t minha-api:1.0 .` |
| Construir sem cache | `docker build --no-cache -t minha-api:1.0 .` |
| Ver camadas | `docker history minha-api:1.0` |
| Inspecionar imagem | `docker image inspect minha-api:1.0` |
| Remover imagem | `docker image rm minha-api:1.0` |
| Criar tag | `docker tag minha-api:1.0 usuario/minha-api:1.0` |
| Login no registry | `docker login` |
| Publicar imagem | `docker push usuario/minha-api:1.0` |
| Baixar do registry | `docker pull usuario/minha-api:1.0` |

## Volumes e persistência

| Quando usar | Comando |
|---|---|
| Criar volume | `docker volume create postgres_data` |
| Listar volumes | `docker volume ls` |
| Inspecionar volume | `docker volume inspect postgres_data` |
| Remover volume | `docker volume rm postgres_data` |
| Remover volumes não usados | `docker volume prune` |
| Usar volume nomeado | `docker run -v postgres_data:/var/lib/postgresql/data postgres:16` |
| Usar bind mount | `docker run -v "$PWD":/app -w /app python:3.12-slim python main.py` |
| Usar tmpfs | `docker run --tmpfs /tmp/sensivel ubuntu` |

## Redes

| Quando usar | Comando |
|---|---|
| Listar redes | `docker network ls` |
| Criar rede bridge | `docker network create backend-net` |
| Criar rede com driver | `docker network create --driver bridge backend-net` |
| Rodar container na rede | `docker run --network backend-net --name api minha-api` |
| Conectar container existente | `docker network connect backend-net api` |
| Desconectar container | `docker network disconnect backend-net api` |
| Inspecionar rede | `docker network inspect backend-net` |
| Remover rede | `docker network rm backend-net` |
| Remover redes não usadas | `docker network prune` |

## Docker Compose

| Quando usar | Comando |
|---|---|
| Subir stack | `docker compose up` |
| Subir em segundo plano | `docker compose up -d` |
| Subir reconstruindo imagem | `docker compose up -d --build` |
| Derrubar stack | `docker compose down` |
| Derrubar removendo volumes | `docker compose down -v` |
| Ver containers da stack | `docker compose ps` |
| Ver logs | `docker compose logs` |
| Acompanhar logs | `docker compose logs -f` |
| Acompanhar logs de um serviço | `docker compose logs -f api` |
| Executar comando em serviço | `docker compose exec api sh` |
| Rodar comando temporário | `docker compose run --rm api python manage.py migrate` |
| Rebuild explícito | `docker compose build` |
| Parar serviços | `docker compose stop` |
| Iniciar serviços parados | `docker compose start` |
| Remover containers parados | `docker compose rm` |

## Limpeza

| Quando usar | Comando |
|---|---|
| Ver uso de disco | `docker system df` |
| Remover containers parados | `docker container prune` |
| Remover imagens dangling | `docker image prune` |
| Remover imagens não usadas | `docker image prune -a` |
| Remover redes não usadas | `docker network prune` |
| Remover volumes não usados | `docker volume prune` |
| Limpeza geral segura | `docker system prune` |
| Limpeza geral mais agressiva | `docker system prune -a` |

## Receitas rápidas para backend

### Subir PostgreSQL local

```bash
docker run -d --name postgres \
  -e POSTGRES_PASSWORD=postgres \
  -e POSTGRES_DB=app \
  -p 5432:5432 \
  -v postgres_data:/var/lib/postgresql/data \
  postgres:16
```

### Subir Redis local

```bash
docker run -d --name redis \
  -p 6379:6379 \
  redis:7-alpine
```

### Rodar script Python sem instalar Python no host

```bash
docker run --rm -v "$PWD":/app -w /app python:3.12-slim python main.py
```

### Entrar no Postgres pelo container

```bash
docker exec -it postgres psql -U postgres -d app
```

### Ver logs de API no Compose

```bash
docker compose logs -f api
```

---

# Referências bibliográficas

- DOCKER. **Docker overview**. Disponível em: <https://docs.docker.com/get-started/docker-overview/>. Acesso em: 31 maio 2026.
- DOCKER. **What is a container?** Disponível em: <https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-a-container/>. Acesso em: 31 maio 2026.
- DOCKER. **Install Docker Engine on Ubuntu**. Disponível em: <https://docs.docker.com/engine/install/ubuntu/>. Acesso em: 31 maio 2026.
- DOCKER. **Linux post-installation steps for Docker Engine**. Disponível em: <https://docs.docker.com/engine/install/linux-postinstall/>. Acesso em: 31 maio 2026.
- DOCKER. **Dockerfile reference**. Disponível em: <https://docs.docker.com/reference/dockerfile/>. Acesso em: 31 maio 2026.
- DOCKER. **Building best practices**. Disponível em: <https://docs.docker.com/build/building/best-practices/>. Acesso em: 31 maio 2026.
- DOCKER. **Storage**. Disponível em: <https://docs.docker.com/engine/storage/>. Acesso em: 31 maio 2026.
- DOCKER. **Volumes**. Disponível em: <https://docs.docker.com/engine/storage/volumes/>. Acesso em: 31 maio 2026.
- DOCKER. **Bind mounts**. Disponível em: <https://docs.docker.com/engine/storage/bind-mounts/>. Acesso em: 31 maio 2026.
- DOCKER. **tmpfs mounts**. Disponível em: <https://docs.docker.com/engine/storage/tmpfs/>. Acesso em: 31 maio 2026.
- DOCKER. **Networking overview**. Disponível em: <https://docs.docker.com/engine/network/>. Acesso em: 31 maio 2026.
- DOCKER. **Bridge network driver**. Disponível em: <https://docs.docker.com/engine/network/drivers/bridge/>. Acesso em: 31 maio 2026.
- DOCKER. **Docker Compose**. Disponível em: <https://docs.docker.com/compose/>. Acesso em: 31 maio 2026.
- DOCKER. **Compose file reference**. Disponível em: <https://docs.docker.com/reference/compose-file/>. Acesso em: 31 maio 2026.
