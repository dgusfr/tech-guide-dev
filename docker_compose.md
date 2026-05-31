# Docker Compose — Orquestrando Ambientes Multi-Contêiner

## Sobre esta apostila

Esta apostila apresenta o **Docker Compose** de forma prática e didática, com foco no dia a dia de desenvolvimento backend. A ideia é entender não apenas o que cada comando faz, mas principalmente **quando usar** cada recurso em cenários reais: subir API com banco de dados, organizar redes, persistir dados, usar variáveis de ambiente, depurar logs e padronizar ambientes de desenvolvimento.

O material parte do problema de gerenciar contêineres manualmente e evolui até um `compose.yaml` mais próximo de um projeto real, com banco de dados, cache, healthcheck, volumes, rede interna e comandos de operação.

## Como estudar por esta apostila

Leia os capítulos na ordem. Sempre que aparecer um bloco de código, tente executar em uma pasta de testes. O Docker Compose é melhor aprendido na prática: subir o ambiente, quebrar alguma coisa, ver logs, ajustar o YAML e subir de novo.

> **Observação de nomenclatura:** versões antigas usavam muito o comando `docker-compose` com hífen. O padrão atual do Docker Compose V2 é usar `docker compose`, integrado à CLI do Docker. Em projetos antigos você ainda pode encontrar `docker-compose`, mas nesta apostila vamos priorizar `docker compose`.

## Índice

1. [Capítulo 1 — O problema que o Docker Compose resolve](#capítulo-1--o-problema-que-o-docker-compose-resolve)
2. [Capítulo 2 — O que é Docker Compose](#capítulo-2--o-que-é-docker-compose)
3. [Capítulo 3 — Anatomia de um arquivo Compose](#capítulo-3--anatomia-de-um-arquivo-compose)
4. [Capítulo 4 — Subindo uma aplicação com banco de dados](#capítulo-4--subindo-uma-aplicação-com-banco-de-dados)
5. [Capítulo 5 — Redes e DNS interno](#capítulo-5--redes-e-dns-interno)
6. [Capítulo 6 — Volumes e persistência de dados](#capítulo-6--volumes-e-persistência-de-dados)
7. [Capítulo 7 — Variáveis de ambiente e arquivos `.env`](#capítulo-7--variáveis-de-ambiente-e-arquivos-env)
8. [Capítulo 8 — `depends_on`, healthcheck e ordem de inicialização](#capítulo-8--depends_on-healthcheck-e-ordem-de-inicialização)
9. [Capítulo 9 — Build de imagens com Compose](#capítulo-9--build-de-imagens-com-compose)
10. [Capítulo 10 — Debug, logs e troubleshooting](#capítulo-10--debug-logs-e-troubleshooting)
11. [Capítulo 11 — Compose em desenvolvimento e produção](#capítulo-11--compose-em-desenvolvimento-e-produção)
12. [Capítulo 12 — Boas práticas para backend](#capítulo-12--boas-práticas-para-backend)
13. [Capítulo 13 — Cheat sheet de Docker Compose](#capítulo-13--cheat-sheet-de-docker-compose)
14. [Referências bibliográficas](#referências-bibliográficas)

---

# Capítulo 1 — O problema que o Docker Compose resolve

Quando estamos aprendendo Docker, normalmente começamos subindo contêineres com comandos `docker run`. Isso funciona bem para testes pequenos, como executar um `nginx`, um `ubuntu` ou uma aplicação simples. O problema aparece quando a aplicação deixa de ser composta por apenas um contêiner.

Em um backend real, é comum ter pelo menos uma API, um banco de dados, talvez um Redis, uma fila, um worker em background e algum serviço auxiliar para testes. Subir tudo isso manualmente exigiria vários comandos longos, com redes, portas, nomes, volumes e variáveis de ambiente.

Imagine um projeto com API e MongoDB. Sem Compose, você teria que criar a rede manualmente, subir o MongoDB, lembrar o nome exato do contêiner, subir a API na mesma rede, mapear portas, configurar variáveis e depois remover tudo manualmente.

```bash
# Criar rede
docker network create minha-bridge

# Subir banco
docker run -d \
  --name meu-mongo \
  --network minha-bridge \
  mongo:4.4.6

# Subir aplicação
docker run -d \
  --name alurabooks \
  --network minha-bridge \
  -p 3000:3000 \
  aluradocker/alura-books:1.0
```

Esse tipo de fluxo tem três problemas principais. Primeiro, é fácil errar um parâmetro. Segundo, é difícil compartilhar o ambiente com outro desenvolvedor. Terceiro, não existe uma documentação executável do ambiente: os comandos ficam soltos em histórico de terminal, README ou anotações.

Ao final deste capítulo, você será capaz de:

- entender por que comandos `docker run` manuais não escalam bem;
- explicar o problema de gerenciar múltiplos contêineres manualmente;
- reconhecer quando faz sentido usar Docker Compose;
- diferenciar uma execução isolada de contêiner de uma aplicação multi-contêiner.

## 1.1 — O problema

O problema central é que aplicações reais geralmente dependem de múltiplos serviços. A API depende do banco. O worker depende da fila. O front-end depende da API. Cada parte precisa de rede, configuração, portas e, em alguns casos, persistência de dados.

Quando cada serviço é iniciado por um comando separado, o ambiente se torna frágil. Um nome errado, uma porta diferente, uma rede esquecida ou uma variável ausente já são suficientes para quebrar a aplicação.

## 1.2 — Exemplo de dificuldade prática

Pense em um desenvolvedor novo entrando no time. Se o projeto usa apenas comandos manuais, ele precisa seguir uma sequência exata de passos. Se a documentação estiver desatualizada, ele perde tempo depurando ambiente em vez de estudar o código.

Com Docker Compose, esse mesmo desenvolvedor pode rodar:

```bash
docker compose up -d
```

E o Compose fica responsável por criar rede, volumes e contêineres definidos no arquivo do projeto.

## 1.3 — O que aconteceu aqui?

O Compose transforma a configuração do ambiente em código. Em vez de depender de memória ou instruções soltas, você descreve o ambiente em YAML. Esse arquivo passa a ser versionado junto com o projeto no Git, servindo como documentação e automação ao mesmo tempo.

## 1.4 — Quando usar Docker Compose?

Use Docker Compose quando o projeto tiver mais de um serviço, quando você quiser padronizar o ambiente de desenvolvimento ou quando precisar que outra pessoa consiga subir a aplicação com poucos comandos.

Exemplos comuns:

- API + PostgreSQL;
- API + MongoDB;
- API + Redis;
- API + RabbitMQ;
- front-end + back-end + banco;
- aplicação + worker + fila;
- ambiente de testes com banco descartável.

## 1.5 — O que pode dar errado?

O erro mais comum é tentar resolver com Compose um problema que ainda não foi entendido com Docker básico. Antes de usar Compose, é importante entender imagens, contêineres, portas, volumes e redes. O Compose não substitui esses conceitos; ele apenas os organiza em um arquivo declarativo.

## 1.6 — Resumo do capítulo

Docker Compose resolve o problema de gerenciar manualmente múltiplos contêineres. Ele permite definir serviços, redes e volumes em um único arquivo, facilitando desenvolvimento, documentação e padronização do ambiente.

---

# Capítulo 2 — O que é Docker Compose

Docker Compose é uma ferramenta usada para **definir e executar aplicações compostas por múltiplos contêineres**. Ele permite declarar, em um arquivo YAML, quais serviços fazem parte da aplicação, quais imagens serão usadas, quais portas serão expostas, quais volumes serão montados e como os serviços se comunicam.

![Visão geral do Docker Compose](./images/docker-compose/compose-visao-geral.svg)

Ao final deste capítulo, você será capaz de:

- explicar o que é Docker Compose;
- entender a relação entre Compose, serviços, redes e volumes;
- diferenciar `docker run` de `docker compose up`;
- reconhecer o Compose como ferramenta de orquestração local.

## 2.1 — O que é Docker Compose?

Docker Compose é uma forma declarativa de descrever uma aplicação Docker. Em vez de executar vários comandos imperativos, você escreve um arquivo dizendo: “minha aplicação precisa destes serviços, com estas imagens, estas portas, estas variáveis e estes volumes”.

A partir disso, o Docker Compose interpreta o arquivo e cria a aplicação.

```bash
docker compose up -d
```

Esse comando lê o `compose.yaml` ou `docker-compose.yml` da pasta atual, cria os recursos necessários e inicia os serviços.

## 2.2 — O que são serviços no Compose?

Um serviço representa um tipo de contêiner dentro da aplicação. Por exemplo, em um projeto backend, você pode ter os serviços `api`, `postgres` e `redis`.

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"

  postgres:
    image: postgres:16

  redis:
    image: redis:7
```

Cada serviço pode gerar um ou mais contêineres. Normalmente, em desenvolvimento, cada serviço roda com uma instância. Em cenários mais avançados, alguns serviços podem ser escalados.

## 2.3 — O que é um projeto Compose?

Um projeto Compose é o conjunto de recursos criados a partir de um arquivo Compose. O nome do projeto normalmente vem do nome da pasta onde o comando é executado, mas pode ser definido manualmente com `-p`.

```bash
docker compose -p minha-api up -d
```

Isso é útil quando você quer subir o mesmo projeto mais de uma vez com nomes diferentes, ou quando quer evitar conflito entre ambientes.

## 2.4 — `compose.yaml` ou `docker-compose.yml`?

Atualmente, o Compose Specification recomenda o uso de `compose.yaml`, mas `docker-compose.yml` continua sendo amplamente aceito e encontrado em muitos projetos. Em empresas, você provavelmente verá os dois nomes.

Nesta apostila, os exemplos usarão `compose.yaml`, mas você pode usar `docker-compose.yml` sem problemas na maioria dos cenários.

## 2.5 — `docker compose` ou `docker-compose`?

O comando moderno é:

```bash
docker compose up
```

O comando antigo era:

```bash
docker-compose up
```

A diferença é que `docker compose` é o plugin atual integrado à CLI do Docker. `docker-compose` era o binário standalone legado. Em projetos atuais, prefira `docker compose`.

## 2.6 — Resumo do capítulo

Docker Compose permite descrever uma aplicação multi-contêiner em YAML. Com um único comando, ele cria e inicia serviços, redes e volumes. Para desenvolvimento backend, é uma das formas mais práticas de padronizar ambientes locais.

---

# Capítulo 3 — Anatomia de um arquivo Compose

O arquivo Compose é escrito em YAML. Ele descreve a aplicação em blocos. Os blocos mais comuns são `services`, `networks`, `volumes`, `configs` e `secrets`. Para começar, os três mais importantes são `services`, `networks` e `volumes`.

Ao final deste capítulo, você será capaz de:

- ler a estrutura básica de um arquivo Compose;
- entender as principais chaves de configuração;
- diferenciar `image` de `build`;
- entender `ports`, `environment`, `volumes`, `networks` e `depends_on`.

## 3.1 — Estrutura mínima

Um arquivo Compose mínimo precisa declarar serviços.

```yaml
services:
  nginx:
    image: nginx:alpine
    ports:
      - "8080:80"
```

Esse arquivo define um serviço chamado `nginx`, baseado na imagem `nginx:alpine`, expondo a porta `80` do contêiner na porta `8080` da máquina host.

Para subir:

```bash
docker compose up -d
```

Para acessar:

```text
http://localhost:8080
```

Para parar e remover:

```bash
docker compose down
```

## 3.2 — `services`

A seção `services` define os componentes da aplicação.

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      APP_ENV: development
```

Nesse exemplo, `api` é o nome do serviço. O Compose vai construir uma imagem a partir do diretório atual, expor a porta `8000` e definir uma variável de ambiente dentro do contêiner.

## 3.3 — `image`

Use `image` quando quiser usar uma imagem pronta.

```yaml
services:
  redis:
    image: redis:7-alpine
```

Esse caso é comum para bancos, caches e serviços auxiliares: PostgreSQL, Redis, RabbitMQ, MongoDB, Nginx etc.

## 3.4 — `build`

Use `build` quando a imagem precisa ser construída a partir do código do projeto.

```yaml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
```

Aqui, o Compose usa o `Dockerfile` da pasta atual para construir a imagem da API.

## 3.5 — `ports`

A chave `ports` mapeia uma porta da máquina host para uma porta do contêiner.

```yaml
ports:
  - "8000:8000"
```

O primeiro valor é a porta do host. O segundo valor é a porta do contêiner.

```text
HOST:CONTAINER
8000:8000
```

Use `ports` quando você precisa acessar o serviço de fora da rede Docker, por exemplo pelo navegador, Postman, Insomnia ou outra aplicação rodando no host.

## 3.6 — `expose`

A chave `expose` documenta uma porta disponível para outros serviços da rede Compose, mas não publica essa porta no host.

```yaml
services:
  api:
    build: .
    expose:
      - "8000"
```

Use `expose` quando outro contêiner precisa acessar o serviço, mas você não quer abrir a porta para sua máquina host.

## 3.7 — `environment`

A chave `environment` define variáveis de ambiente no contêiner.

```yaml
environment:
  APP_ENV: development
  LOG_LEVEL: debug
```

Essas variáveis são lidas pela aplicação em tempo de execução. Em uma API Python, por exemplo, você pode ler com `os.getenv()`.

## 3.8 — `env_file`

A chave `env_file` carrega variáveis de um arquivo externo.

```yaml
env_file:
  - .env
```

Isso ajuda a evitar que o Compose fique gigante e facilita separar configurações de desenvolvimento, teste e produção.

## 3.9 — `volumes`

A chave `volumes` monta dados dentro do contêiner.

```yaml
volumes:
  - postgres-data:/var/lib/postgresql/data
```

Nesse exemplo, o volume `postgres-data` persiste os dados do PostgreSQL mesmo que o contêiner seja removido.

## 3.10 — `networks`

A chave `networks` define em quais redes o serviço ficará conectado.

```yaml
networks:
  - backend
```

Serviços na mesma rede conseguem se comunicar pelo nome do serviço.

## 3.11 — `depends_on`

A chave `depends_on` define ordem de inicialização.

```yaml
depends_on:
  - postgres
```

Isso indica que o serviço atual depende do serviço `postgres`. O Compose inicia o `postgres` antes, mas é importante entender que iniciar o contêiner não significa que o banco já está pronto para aceitar conexões. Esse ponto será aprofundado no capítulo sobre healthcheck.

## 3.12 — Exemplo básico completo

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      APP_ENV: development
    depends_on:
      - postgres
    networks:
      - backend

  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: app_db
      POSTGRES_USER: app_user
      POSTGRES_PASSWORD: app_password
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - backend

networks:
  backend:
    driver: bridge

volumes:
  postgres-data:
```

## 3.13 — O que aconteceu no código?

A aplicação tem dois serviços: `api` e `postgres`. Ambos estão na rede `backend`, então a API pode acessar o banco usando o hostname `postgres`. O banco usa um volume nomeado para persistir os dados. A API expõe a porta `8000` para o host, permitindo acesso via navegador ou cliente HTTP.

## 3.14 — O que pode dar errado?

O erro mais comum em YAML é a indentação. YAML não usa chaves como JSON; ele usa espaços. Se a indentação estiver errada, o Compose pode interpretar a estrutura de forma incorreta ou nem conseguir ler o arquivo.

Outro erro comum é misturar `ports` e `expose`. Lembre-se: `ports` publica a porta para fora; `expose` apenas documenta/disponibiliza internamente.

## 3.15 — Resumo do capítulo

A estrutura principal do Compose está em `services`, `networks` e `volumes`. Serviços representam contêineres da aplicação. Redes permitem comunicação interna. Volumes persistem dados. Essas três partes são a base para quase qualquer ambiente Docker Compose.

---

# Capítulo 4 — Subindo uma aplicação com banco de dados

Agora vamos montar um exemplo prático semelhante ao que você usaria no dia a dia: uma API backend com PostgreSQL e Redis. Mesmo que o seu projeto use MongoDB, MySQL ou RabbitMQ, a lógica será muito parecida.

Ao final deste capítulo, você será capaz de:

- criar um `compose.yaml` para uma aplicação backend;
- conectar API, banco e cache na mesma rede;
- entender como a API encontra o banco pelo nome do serviço;
- subir e derrubar o ambiente com segurança.

## 4.1 — Cenário

Vamos imaginar uma API Python. Ela depende de:

- PostgreSQL para persistência principal;
- Redis para cache ou filas simples;
- porta `8000` exposta para acesso local.

A estrutura do projeto poderia ser:

```text
meu-projeto/
├── app/
│   └── main.py
├── Dockerfile
├── compose.yaml
└── .env
```

## 4.2 — Arquivo `.env`

```env
POSTGRES_DB=app_db
POSTGRES_USER=app_user
POSTGRES_PASSWORD=app_password
DATABASE_URL=postgresql://app_user:app_password@postgres:5432/app_db
REDIS_URL=redis://redis:6379/0
```

O ponto mais importante aqui é o host do banco: `postgres`. Esse é o nome do serviço no Compose. O Redis também será acessado pelo nome do serviço: `redis`.

## 4.3 — `compose.yaml`

```yaml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: minha-api
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      - postgres
      - redis
    networks:
      - backend

  postgres:
    image: postgres:16-alpine
    container_name: minha-api-postgres
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - postgres-data:/var/lib/postgresql/data
    networks:
      - backend

  redis:
    image: redis:7-alpine
    container_name: minha-api-redis
    networks:
      - backend

networks:
  backend:
    driver: bridge

volumes:
  postgres-data:
```

## 4.4 — Executando

Na pasta do projeto:

```bash
docker compose up -d
```

Verifique os serviços:

```bash
docker compose ps
```

Acompanhe os logs:

```bash
docker compose logs -f
```

Acompanhe apenas a API:

```bash
docker compose logs -f api
```

## 4.5 — Testando a aplicação

Se a API estiver escutando na porta `8000`, acesse:

```text
http://localhost:8000
```

Ou teste com `curl`:

```bash
curl http://localhost:8000
```

## 4.6 — Entrando em um serviço

Para abrir um shell dentro da API:

```bash
docker compose exec api sh
```

Se a imagem tiver Bash:

```bash
docker compose exec api bash
```

Para entrar no PostgreSQL:

```bash
docker compose exec postgres psql -U app_user -d app_db
```

## 4.7 — Parando o ambiente

Para parar e remover contêineres e rede:

```bash
docker compose down
```

Para também apagar os volumes:

```bash
docker compose down -v
```

Use `down -v` com cuidado, porque ele apaga dados persistidos em volumes nomeados do projeto.

## 4.8 — O que aconteceu no código?

O Compose criou três serviços conectados à mesma rede `backend`. A API pode acessar o PostgreSQL pelo hostname `postgres` e o Redis pelo hostname `redis`. O volume `postgres-data` mantém os dados do banco fora da camada descartável do contêiner.

## 4.9 — Quando usar isso?

Esse tipo de Compose é útil em praticamente qualquer projeto backend local. Ele evita instalar PostgreSQL e Redis direto na máquina e garante que todos os devs do projeto usem versões iguais dos serviços.

## 4.10 — Resumo do capítulo

Com Docker Compose, você consegue subir uma aplicação backend com banco e cache usando um único arquivo e um único comando. O ponto mais importante é entender que os serviços se comunicam pelo nome definido em `services`.

---

# Capítulo 5 — Redes e DNS interno

Quando o Compose sobe uma aplicação, ele cria uma rede padrão para o projeto. Todos os serviços entram nessa rede e conseguem se encontrar pelo nome do serviço. Esse mecanismo funciona como um DNS interno.

![DNS interno do Compose](./images/docker-compose/compose-rede-dns.svg)

Ao final deste capítulo, você será capaz de:

- entender como serviços se comunicam no Compose;
- diferenciar acesso interno de acesso externo;
- saber quando usar `ports`;
- evitar depender de IP de contêiner.

## 5.1 — Comunicação por nome do serviço

Dentro da rede Compose, um serviço não precisa saber o IP do outro. Ele usa o nome do serviço.

```yaml
services:
  api:
    environment:
      DATABASE_URL: postgresql://app_user:app_password@postgres:5432/app_db

  postgres:
    image: postgres:16-alpine
```

A API acessa o banco usando `postgres`, porque esse é o nome do serviço.

## 5.2 — Por que não usar IP?

O IP de um contêiner é dinâmico. Se o contêiner for removido e recriado, ele pode receber outro IP. Por isso, configurar aplicações com IP fixo de contêiner deixa o ambiente frágil.

Use nomes de serviço, não IPs.

## 5.3 — Acesso interno x acesso externo

Há duas formas de acesso:

```text
Outro contêiner acessando a API: http://api:8000
Sua máquina acessando a API: http://localhost:8000
```

O primeiro caso acontece dentro da rede Docker. O segundo caso precisa de `ports`.

## 5.4 — `ports`

Use `ports` quando algo fora do Docker precisa acessar o contêiner.

```yaml
ports:
  - "8000:8000"
```

Isso permite acessar a API pela máquina host em `localhost:8000`.

## 5.5 — Banco de dados precisa expor porta?

Em desenvolvimento, às vezes é útil expor o banco para usar DBeaver, DataGrip ou psql local.

```yaml
postgres:
  image: postgres:16-alpine
  ports:
    - "5432:5432"
```

Mas, se apenas a API precisa acessar o banco, você não precisa expor a porta do banco no host. Internamente, a API consegue acessar `postgres:5432` sem `ports`.

## 5.6 — Rede customizada

Você pode deixar o Compose criar a rede automaticamente ou declarar explicitamente.

```yaml
networks:
  backend:
    driver: bridge
```

Declarar explicitamente melhora a legibilidade quando o projeto cresce.

## 5.7 — Cuidado com `container_name`

`container_name` define um nome fixo para o contêiner.

```yaml
container_name: minha-api-postgres
```

Ele é útil em estudos e testes, mas em projetos maiores pode atrapalhar escalabilidade e múltiplas execuções do mesmo projeto, porque nomes de contêiner precisam ser únicos no Docker host.

Em projetos profissionais, muitas vezes é melhor deixar o Compose gerar os nomes automaticamente.

## 5.8 — Resumo do capítulo

No Compose, serviços na mesma rede se comunicam pelo nome. Evite IPs de contêiner. Use `ports` apenas quando precisar acessar o serviço a partir da máquina host. Para comunicação interna, o nome do serviço é suficiente.

---

# Capítulo 6 — Volumes e persistência de dados

Contêineres são descartáveis. Isso significa que você deve conseguir remover e recriar um contêiner sem perder o que realmente importa. Para banco de dados, os dados não devem ficar apenas dentro da camada do contêiner. Eles precisam estar em volumes.

![Volumes no Docker Compose](./images/docker-compose/compose-volumes.svg)

Ao final deste capítulo, você será capaz de:

- explicar por que contêiner não deve ser tratado como armazenamento definitivo;
- usar volumes nomeados no Compose;
- diferenciar volume nomeado de bind mount;
- entender quando `docker compose down -v` apaga dados.

## 6.1 — O problema

Se você subir um banco sem volume, gravar dados e depois remover o contêiner, os dados podem ser perdidos. Isso acontece porque a camada gravável do contêiner é descartável.

## 6.2 — Volume nomeado

Volume nomeado é o mecanismo recomendado para persistir dados de serviços como PostgreSQL, MySQL, MongoDB e Redis com AOF/RDB.

```yaml
services:
  postgres:
    image: postgres:16-alpine
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  postgres-data:
```

O lado esquerdo é o nome do volume. O lado direito é o caminho dentro do contêiner.

```text
postgres-data:/var/lib/postgresql/data
```

## 6.3 — Bind mount

Bind mount liga uma pasta do host a uma pasta do contêiner.

```yaml
services:
  api:
    volumes:
      - ./app:/app
```

Isso é muito útil em desenvolvimento, porque permite alterar o código no host e refletir dentro do contêiner.

## 6.4 — Volume nomeado x bind mount

| Tipo | Melhor uso | Exemplo |
|---|---|---|
| Volume nomeado | Dados gerenciados pelo Docker | dados do PostgreSQL |
| Bind mount | Código-fonte em desenvolvimento | `./app:/app` |

## 6.5 — Listando volumes

```bash
docker volume ls
```

## 6.6 — Inspecionando um volume

```bash
docker volume inspect nome_do_volume
```

Em Compose, os volumes geralmente recebem prefixo com o nome do projeto.

## 6.7 — Removendo volumes

Remover contêineres e rede:

```bash
docker compose down
```

Remover também volumes do projeto:

```bash
docker compose down -v
```

Use `down -v` quando quiser zerar o ambiente local, por exemplo, recriar banco do zero. Não use se quiser preservar dados.

## 6.8 — O que pode dar errado?

Um erro comum é achar que `docker compose down` sempre apaga dados. Ele remove contêineres e redes, mas volumes nomeados permanecem por padrão. Os dados só são removidos se você usar `-v` ou remover volumes manualmente.

Outro erro comum é montar bind mount por cima de uma pasta importante da imagem. Isso pode esconder arquivos que já existiam dentro do contêiner.

## 6.9 — Resumo do capítulo

Use volumes nomeados para persistir dados de bancos. Use bind mounts para desenvolvimento com código local. Entenda bem a diferença entre `docker compose down` e `docker compose down -v`.

---

# Capítulo 7 — Variáveis de ambiente e arquivos `.env`

Aplicações backend geralmente precisam de configurações: URL do banco, senha, usuário, ambiente, chave de API, porta e configurações de log. No Compose, essas informações podem ser definidas com `environment`, `env_file` e `.env`.

Ao final deste capítulo, você será capaz de:

- usar variáveis de ambiente no Compose;
- entender interpolação com `.env`;
- diferenciar `.env` usado pelo Compose de variáveis dentro do contêiner;
- evitar versionar segredos reais.

## 7.1 — `environment`

A forma direta é usar `environment`.

```yaml
services:
  api:
    image: minha-api:latest
    environment:
      APP_ENV: development
      LOG_LEVEL: debug
```

Essas variáveis ficam disponíveis dentro do contêiner.

## 7.2 — `env_file`

Também é possível carregar variáveis de um arquivo.

```yaml
services:
  api:
    image: minha-api:latest
    env_file:
      - .env
```

Arquivo `.env`:

```env
APP_ENV=development
LOG_LEVEL=debug
DATABASE_URL=postgresql://app_user:app_password@postgres:5432/app_db
```

## 7.3 — Interpolação de variáveis

O Compose também usa `.env` para substituir valores no YAML.

`.env`:

```env
POSTGRES_DB=app_db
POSTGRES_USER=app_user
POSTGRES_PASSWORD=app_password
```

`compose.yaml`:

```yaml
services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
```

Nesse caso, o Compose lê o `.env` e troca `${POSTGRES_DB}` pelo valor correspondente.

## 7.4 — `.env` deve ir para o Git?

Depende. Em geral, arquivos com segredos reais não devem ser versionados. Uma prática comum é versionar um `.env.example` com valores falsos ou genéricos.

`.env.example`:

```env
POSTGRES_DB=app_db
POSTGRES_USER=app_user
POSTGRES_PASSWORD=change_me
DATABASE_URL=postgresql://app_user:change_me@postgres:5432/app_db
```

E adicionar o `.env` real ao `.gitignore`:

```gitignore
.env
```

## 7.5 — Segredos sensíveis

Variáveis de ambiente são práticas, mas não são o mecanismo mais seguro para segredos em produção. Para senhas, tokens e chaves sensíveis em ambientes reais, avalie mecanismos como Docker secrets, Kubernetes Secrets, AWS Secrets Manager, HashiCorp Vault ou secret manager equivalente do seu provedor.

## 7.6 — O que pode dar errado?

O erro mais comum é achar que `env_file` e interpolação são exatamente a mesma coisa. `env_file` injeta variáveis dentro do contêiner. Já o `.env` de interpolação é usado pelo Compose para substituir valores no arquivo YAML antes de criar os serviços.

Na prática, muitas vezes o mesmo `.env` acaba servindo para as duas coisas, mas os conceitos são diferentes.

## 7.7 — Resumo do capítulo

Use `environment` para poucas variáveis simples. Use `env_file` para organizar configurações em arquivo. Use `.env.example` para documentar variáveis esperadas sem expor segredos reais.

---

# Capítulo 8 — `depends_on`, healthcheck e ordem de inicialização

O `depends_on` é um dos recursos mais usados e mais mal interpretados do Compose. Ele ajuda a controlar ordem de inicialização, mas não garante sozinho que uma aplicação dependente já está pronta para uso.

![depends_on e healthcheck](./images/docker-compose/compose-depends-healthcheck.svg)

Ao final deste capítulo, você será capaz de:

- entender o que `depends_on` faz;
- entender o que `depends_on` não faz sozinho;
- configurar healthcheck;
- usar `condition: service_healthy`;
- explicar por que aplicações ainda precisam de retry.

## 8.1 — O problema

Uma API pode depender de um banco. O Compose pode iniciar o banco antes da API. Porém, o contêiner do banco estar iniciado não significa que o processo do banco já está aceitando conexões.

Esse é o clássico erro:

```text
connection refused
```

A API sobe rápido, tenta conectar no banco, mas o banco ainda está inicializando.

## 8.2 — `depends_on` simples

```yaml
services:
  api:
    build: .
    depends_on:
      - postgres

  postgres:
    image: postgres:16-alpine
```

Isso define ordem: o Compose inicia `postgres` antes de `api`.

## 8.3 — Healthcheck

Healthcheck é uma verificação de saúde do contêiner.

```yaml
services:
  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: app_db
      POSTGRES_USER: app_user
      POSTGRES_PASSWORD: app_password
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app_user -d app_db"]
      interval: 5s
      timeout: 5s
      retries: 5
```

Aqui, o Compose verifica se o PostgreSQL está pronto usando `pg_isready`.

## 8.4 — `depends_on` com condição

```yaml
services:
  api:
    build: .
    depends_on:
      postgres:
        condition: service_healthy

  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: app_db
      POSTGRES_USER: app_user
      POSTGRES_PASSWORD: app_password
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app_user -d app_db"]
      interval: 5s
      timeout: 5s
      retries: 5
```

Agora a API só inicia depois que o serviço `postgres` estiver saudável.

## 8.5 — Isso resolve tudo?

Não. Healthcheck melhora muito a inicialização, mas não elimina a necessidade de resiliência na aplicação.

Mesmo depois de iniciado, o banco pode cair, reiniciar ou ficar temporariamente indisponível. Por isso, aplicações reais devem implementar retry, timeout e tratamento adequado de erro.

## 8.6 — Exemplo com API, Postgres e Redis

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - backend

  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - postgres-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - backend

  redis:
    image: redis:7-alpine
    networks:
      - backend

networks:
  backend:

volumes:
  postgres-data:
```

## 8.7 — O que pode dar errado?

Algumas imagens não possuem o comando usado no healthcheck. Por exemplo, se você configurar um healthcheck com `curl`, mas a imagem não tiver `curl`, o teste falhará. Sempre use comandos disponíveis na imagem ou instale as ferramentas necessárias.

## 8.8 — Resumo do capítulo

`depends_on` simples controla ordem de inicialização. Healthcheck verifica se um serviço está saudável. `condition: service_healthy` ajuda a evitar que a API suba antes do banco estar pronto, mas a aplicação ainda precisa ser resiliente contra falhas posteriores.

---

# Capítulo 9 — Build de imagens com Compose

Até agora vimos muitos exemplos usando `image`, que baixa imagens prontas. Porém, em um projeto backend, normalmente você precisa construir a imagem da sua própria aplicação. Para isso, usamos `build`.

Ao final deste capítulo, você será capaz de:

- construir imagens com Compose;
- entender `context`, `dockerfile` e `target`;
- saber quando usar `docker compose build`;
- diferenciar `up`, `up --build` e `build --no-cache`.

## 9.1 — Dockerfile da API

Exemplo simples para uma API Python:

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8000

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```

## 9.2 — Compose usando `build`

```yaml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
```

## 9.3 — Construir a imagem

```bash
docker compose build
```

## 9.4 — Subir construindo se necessário

```bash
docker compose up -d --build
```

Esse comando é muito comum no desenvolvimento. Ele garante que a imagem da aplicação seja reconstruída antes de subir.

## 9.5 — Build sem cache

```bash
docker compose build --no-cache
```

Use quando você suspeita que o cache está reaproveitando camadas antigas ou quando precisa garantir uma reconstrução totalmente limpa.

## 9.6 — `target` em Dockerfile multi-stage

Se seu Dockerfile tiver múltiplos estágios, você pode escolher um estágio.

```yaml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
      target: development
```

Isso é útil para ter uma imagem de desenvolvimento com ferramentas extras e uma imagem de produção mais enxuta.

## 9.7 — Build args

Argumentos de build são valores disponíveis durante a construção da imagem.

```yaml
services:
  api:
    build:
      context: .
      args:
        APP_VERSION: "1.0.0"
```

No Dockerfile:

```dockerfile
ARG APP_VERSION
ENV APP_VERSION=$APP_VERSION
```

## 9.8 — O que pode dar errado?

Um erro comum é alterar o código e rodar apenas:

```bash
docker compose up -d
```

Se a imagem não for reconstruída e você não estiver usando bind mount, a alteração pode não aparecer. Nesses casos, rode:

```bash
docker compose up -d --build
```

## 9.9 — Resumo do capítulo

Use `image` para imagens prontas e `build` para a aplicação do projeto. Em desenvolvimento, `docker compose up -d --build` é um comando muito útil. Quando houver comportamento estranho causado por cache, use `docker compose build --no-cache`.

---

# Capítulo 10 — Debug, logs e troubleshooting

Saber subir um ambiente com Compose é importante, mas saber diagnosticar problemas é ainda mais importante. No dia a dia, você vai usar muito `ps`, `logs`, `exec`, `config`, `restart` e `down`.

Ao final deste capítulo, você será capaz de:

- verificar status dos serviços;
- ler logs corretamente;
- entrar em contêineres;
- validar o arquivo Compose;
- resolver erros comuns.

## 10.1 — Ver status

```bash
docker compose ps
```

Mostra os serviços do projeto, status, portas e nomes dos contêineres.

## 10.2 — Ver logs

Todos os serviços:

```bash
docker compose logs
```

Acompanhar em tempo real:

```bash
docker compose logs -f
```

Apenas um serviço:

```bash
docker compose logs -f api
```

Últimas linhas:

```bash
docker compose logs --tail=100 api
```

## 10.3 — Entrar em um contêiner

```bash
docker compose exec api sh
```

Com Bash:

```bash
docker compose exec api bash
```

Executar comando direto:

```bash
docker compose exec api python --version
```

## 10.4 — Rodar comando temporário

```bash
docker compose run --rm api python manage.py migrate
```

Esse padrão é muito usado para migrations, shells e comandos administrativos.

## 10.5 — Validar o Compose final

```bash
docker compose config
```

Esse comando mostra o arquivo Compose renderizado, com variáveis interpoladas. É excelente para depurar `.env` e erros de YAML.

## 10.6 — Reiniciar serviço

```bash
docker compose restart api
```

## 10.7 — Recriar apenas um serviço

```bash
docker compose up -d --build api
```

## 10.8 — Erros comuns

### Porta já está em uso

Erro típico:

```text
Bind for 0.0.0.0:8000 failed: port is already allocated
```

Solução: troque a porta do host.

```yaml
ports:
  - "8001:8000"
```

Agora a aplicação fica acessível em `localhost:8001`.

### Banco não conecta

Verifique se a aplicação está usando o nome do serviço, não `localhost`.

Errado dentro de contêiner:

```text
postgresql://user:pass@localhost:5432/db
```

Correto dentro da rede Compose:

```text
postgresql://user:pass@postgres:5432/db
```

Dentro de um contêiner, `localhost` aponta para o próprio contêiner, não para o host nem para outro serviço.

### Variável de ambiente não entrou

Use:

```bash
docker compose config
```

E confira se a variável apareceu no serviço correto.

### Contêiner sai imediatamente

Veja os logs:

```bash
docker compose logs api
```

Se o processo principal encerra, o contêiner encerra. Contêiner precisa ter um processo em primeiro plano.

### Mudança no código não apareceu

Se você não usa bind mount, precisa reconstruir a imagem:

```bash
docker compose up -d --build
```

## 10.9 — Resumo do capítulo

No dia a dia, debug com Compose passa por cinco comandos principais: `ps`, `logs`, `exec`, `config` e `restart`. Saber interpretar logs e entender que `localhost` dentro de contêiner aponta para o próprio contêiner resolve muitos problemas.

---

# Capítulo 11 — Compose em desenvolvimento e produção

Docker Compose é excelente para desenvolvimento local, ambientes de teste, laboratórios e stacks simples. Ele também pode ser usado em servidores pequenos, mas não substitui automaticamente orquestradores de produção como Kubernetes ou Docker Swarm.

Ao final deste capítulo, você será capaz de:

- entender o papel do Compose em desenvolvimento;
- conhecer limites do Compose em produção;
- usar override files;
- entender profiles;
- saber o que é a seção `deploy`.

## 11.1 — Compose em desenvolvimento

Em desenvolvimento, Compose brilha porque permite subir dependências locais rapidamente.

Exemplo:

```bash
docker compose up -d postgres redis rabbitmq
```

Você pode rodar a API localmente na máquina e manter apenas banco/cache/fila em contêineres. Também pode rodar tudo em contêineres.

## 11.2 — Override files

Você pode combinar arquivos.

```bash
docker compose -f compose.yaml -f compose.dev.yaml up -d
```

Exemplo de `compose.dev.yaml`:

```yaml
services:
  api:
    volumes:
      - ./app:/app/app
    environment:
      APP_ENV: development
```

Isso permite ter uma base comum e personalizações por ambiente.

## 11.3 — Profiles

Profiles permitem ativar serviços opcionais.

```yaml
services:
  api:
    build: .

  adminer:
    image: adminer
    ports:
      - "8080:8080"
    profiles:
      - tools
```

Subir apenas serviços padrão:

```bash
docker compose up -d
```

Subir também ferramentas:

```bash
docker compose --profile tools up -d
```

Isso é útil para ferramentas como Adminer, Mailhog, Prometheus, Grafana ou workers opcionais.

## 11.4 — Watch mode

Versões modernas do Docker Compose possuem recursos voltados ao desenvolvimento, como `watch`, que ajuda a sincronizar alterações e reconstruir serviços conforme o código muda.

Exemplo conceitual:

```yaml
services:
  api:
    build: .
    develop:
      watch:
        - action: sync
          path: ./app
          target: /app/app
        - action: rebuild
          path: ./requirements.txt
```

Executar:

```bash
docker compose watch
```

Esse recurso é útil quando você quer um fluxo de desenvolvimento mais automatizado, mas nem sempre é necessário. Para muitos projetos backend, um bind mount simples já resolve.

## 11.5 — Compose em produção

Compose pode ser usado em produção simples, especialmente em um único servidor. Porém, em ambientes mais críticos, você normalmente precisa de recursos como:

- alta disponibilidade;
- autoscaling;
- rolling updates robustos;
- service discovery avançado;
- gerenciamento de segredos;
- observabilidade integrada;
- self-healing;
- múltiplos nós.

Nesses casos, Kubernetes, Docker Swarm ou serviços gerenciados de nuvem tendem a ser escolhas mais adequadas.

## 11.6 — A seção `deploy`

A seção `deploy` faz parte da especificação Compose e descreve configurações de implantação, como réplicas, limites de recursos e política de restart.

```yaml
services:
  api:
    image: minha-api:latest
    deploy:
      replicas: 3
      resources:
        limits:
          cpus: "0.50"
          memory: 512M
      restart_policy:
        condition: on-failure
```

Em ambientes locais com `docker compose up`, nem todo recurso de `deploy` terá o mesmo efeito que teria em um orquestrador. Por isso, trate `deploy` como um assunto mais avançado e sempre confirme o comportamento no ambiente real onde será executado.

## 11.7 — Resumo do capítulo

Use Compose para desenvolvimento, testes e stacks locais. Para produção crítica, avalie orquestração mais robusta. Use arquivos múltiplos, profiles e watch mode para adaptar o ambiente ao fluxo de desenvolvimento.

---

# Capítulo 12 — Boas práticas para backend

Este capítulo reúne práticas simples que evitam muitos problemas em projetos Docker Compose usados por times backend.

Ao final deste capítulo, você será capaz de:

- organizar melhor arquivos Compose;
- evitar exposição desnecessária de portas;
- proteger segredos;
- melhorar a experiência de novos devs;
- escrever Compose mais previsível.

## 12.1 — Prefira nomes de serviço claros

Bom:

```yaml
services:
  api:
  postgres:
  redis:
```

Ruim:

```yaml
services:
  app1:
  db1:
  cache1:
```

Nomes claros melhoram legibilidade e funcionam como hostnames internos.

## 12.2 — Evite expor portas desnecessárias

Se só a API precisa acessar o banco, não exponha o PostgreSQL no host.

```yaml
postgres:
  image: postgres:16-alpine
  # Sem ports
```

Exponha a porta do banco apenas quando precisar acessar com ferramenta externa.

## 12.3 — Use `.env.example`

Versione um exemplo:

```env
POSTGRES_DB=app_db
POSTGRES_USER=app_user
POSTGRES_PASSWORD=change_me
DATABASE_URL=postgresql://app_user:change_me@postgres:5432/app_db
```

E ignore o `.env` real:

```gitignore
.env
```

## 12.4 — Use volumes nomeados para bancos

```yaml
volumes:
  postgres-data:
```

Evite armazenar dados importantes apenas na camada do contêiner.

## 12.5 — Use healthcheck para dependências críticas

Bancos e filas podem demorar para ficar prontos. Healthcheck reduz erros de inicialização.

## 12.6 — Não dependa de `container_name` sem necessidade

Em muitos casos, o nome do serviço é suficiente. `container_name` pode atrapalhar múltiplas execuções do projeto e escalabilidade.

## 12.7 — Documente comandos no README

Um bom README para projeto com Compose deve ter pelo menos:

```bash
cp .env.example .env
docker compose up -d --build
docker compose logs -f api
docker compose down
```

## 12.8 — Crie comandos auxiliares com Makefile

Exemplo:

```makefile
up:
	docker compose up -d --build

down:
	docker compose down

logs:
	docker compose logs -f api

migrate:
	docker compose run --rm api alembic upgrade head
```

Assim, novos devs podem usar:

```bash
make up
make logs
make migrate
```

## 12.9 — Compose final recomendado para backend Python

```yaml
services:
  api:
    build:
      context: .
      dockerfile: Dockerfile
    ports:
      - "8000:8000"
    env_file:
      - .env
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_started
    networks:
      - backend

  postgres:
    image: postgres:16-alpine
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    volumes:
      - postgres-data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 5s
      timeout: 5s
      retries: 5
    networks:
      - backend

  redis:
    image: redis:7-alpine
    networks:
      - backend

networks:
  backend:
    driver: bridge

volumes:
  postgres-data:
```

## 12.10 — Resumo do capítulo

Boas práticas em Compose são sobre previsibilidade: nomes claros, redes internas, volumes nomeados, healthchecks, `.env.example`, poucos comandos e README objetivo. Um bom Compose reduz atrito no time e evita que cada desenvolvedor configure o ambiente de um jeito diferente.

---

# Capítulo 13 — Cheat sheet de Docker Compose

Esta seção é uma cola rápida para uso no dia a dia.

## 13.1 — Subir e parar ambiente

```bash
# Subir serviços em primeiro plano
docker compose up

# Subir em segundo plano
docker compose up -d

# Subir reconstruindo imagens
docker compose up -d --build

# Parar e remover contêineres e redes
docker compose down

# Parar e remover também volumes
docker compose down -v
```

## 13.2 — Status e logs

```bash
# Ver serviços do projeto
docker compose ps

# Ver logs de todos os serviços
docker compose logs

# Acompanhar logs em tempo real
docker compose logs -f

# Acompanhar logs de um serviço
docker compose logs -f api

# Ver últimas 100 linhas
docker compose logs --tail=100 api
```

## 13.3 — Executar comandos dentro de serviços

```bash
# Abrir shell
docker compose exec api sh

# Abrir bash, se existir
docker compose exec api bash

# Rodar comando dentro do serviço
docker compose exec api python --version

# Rodar comando temporário em novo contêiner
docker compose run --rm api python manage.py migrate

# Rodar testes
docker compose run --rm api pytest
```

## 13.4 — Build

```bash
# Construir imagens
docker compose build

# Construir sem cache
docker compose build --no-cache

# Construir apenas um serviço
docker compose build api

# Subir apenas um serviço com build
docker compose up -d --build api
```

## 13.5 — Imagens

```bash
# Baixar imagens definidas no Compose
docker compose pull

# Enviar imagens para registry, se configurado
docker compose push
```

## 13.6 — Serviços específicos

```bash
# Subir apenas banco e redis
docker compose up -d postgres redis

# Reiniciar API
docker compose restart api

# Parar um serviço
docker compose stop api

# Iniciar um serviço parado
docker compose start api

# Remover contêineres parados do projeto
docker compose rm
```

## 13.7 — Validação e configuração

```bash
# Validar e renderizar configuração final
docker compose config

# Usar outro arquivo Compose
docker compose -f compose.dev.yaml up -d

# Combinar arquivos
docker compose -f compose.yaml -f compose.dev.yaml up -d

# Definir nome do projeto
docker compose -p minha-api up -d
```

## 13.8 — Profiles

```bash
# Subir com profile tools
docker compose --profile tools up -d

# Subir com múltiplos profiles
docker compose --profile tools --profile observability up -d
```

## 13.9 — Volumes

```bash
# Listar volumes Docker
docker volume ls

# Inspecionar volume
docker volume inspect nome_do_volume

# Remover volumes não usados
docker volume prune

# CUIDADO: remove volumes do projeto
docker compose down -v
```

## 13.10 — Redes

```bash
# Listar redes Docker
docker network ls

# Inspecionar rede
docker network inspect nome_da_rede
```

## 13.11 — Receitas rápidas para backend

### Subir ambiente local completo

```bash
cp .env.example .env
docker compose up -d --build
docker compose logs -f api
```

### Rodar migration

```bash
docker compose run --rm api alembic upgrade head
```

Ou, em Django:

```bash
docker compose run --rm api python manage.py migrate
```

### Rodar testes

```bash
docker compose run --rm api pytest
```

### Resetar banco local

```bash
docker compose down -v
docker compose up -d --build
```

### Entrar no Postgres

```bash
docker compose exec postgres psql -U app_user -d app_db
```

### Ver variáveis renderizadas

```bash
docker compose config
```

## 13.12 — Quando usar cada comando?

| Situação | Comando recomendado |
|---|---|
| Quero subir tudo | `docker compose up -d` |
| Alterei Dockerfile/dependências | `docker compose up -d --build` |
| Quero ver logs da API | `docker compose logs -f api` |
| Quero entrar na API | `docker compose exec api sh` |
| Quero rodar migration | `docker compose run --rm api alembic upgrade head` |
| Quero parar tudo sem apagar dados | `docker compose down` |
| Quero zerar banco local | `docker compose down -v` |
| Quero validar YAML e `.env` | `docker compose config` |
| Quero subir ferramentas opcionais | `docker compose --profile tools up -d` |
| Quero usar arquivo de dev | `docker compose -f compose.yaml -f compose.dev.yaml up -d` |

---

# Exercícios

## Exercício 1 — API + banco

Crie um `compose.yaml` com dois serviços: `api` e `postgres`. A API deve expor a porta `8000`. O banco deve usar volume nomeado.

## Exercício 2 — Redis

Adicione um serviço `redis` ao Compose. Configure a API para receber uma variável `REDIS_URL=redis://redis:6379/0`.

## Exercício 3 — Healthcheck

Adicione healthcheck ao PostgreSQL e configure a API para depender de `postgres` com `condition: service_healthy`.

## Exercício 4 — `.env.example`

Crie um `.env.example` com as variáveis necessárias para subir o projeto e adicione `.env` ao `.gitignore`.

## Exercício 5 — Debug

Suba a aplicação, veja os logs da API, entre no contêiner e execute um comando que mostre as variáveis de ambiente disponíveis.

---

# Desafios

## Desafio 1 — Compose para projeto real

Pegue uma API backend sua e escreva um `compose.yaml` completo para ela, incluindo banco, cache, volume, rede e `.env.example`.

## Desafio 2 — Separação por ambiente

Crie dois arquivos:

```text
compose.yaml
compose.dev.yaml
```

No arquivo base, deixe a configuração comum. No arquivo de desenvolvimento, adicione bind mount e variáveis específicas de dev.

## Desafio 3 — Makefile

Crie um Makefile com comandos:

```text
make up
make down
make logs
make test
make migrate
```

---

# Fixando o conhecimento

1. Qual é a diferença entre `docker run` e `docker compose up`?
2. Por que serviços no Compose conseguem se comunicar pelo nome?
3. Quando usar `ports`?
4. Qual é a diferença entre volume nomeado e bind mount?
5. O que `depends_on` garante?
6. O que `depends_on` não garante sozinho?
7. Para que serve `docker compose config`?
8. Quando usar `docker compose down -v`?
9. Por que não é ideal colocar senhas reais no Git?
10. Por que `localhost` dentro de um contêiner pode causar erro de conexão?

---

# Referências bibliográficas

- DOCKER. **Docker Compose**. Disponível em: <https://docs.docker.com/compose/>. Acesso em: 31 maio 2026.
- DOCKER. **Compose file reference**. Disponível em: <https://docs.docker.com/reference/compose-file/>. Acesso em: 31 maio 2026.
- DOCKER. **How Compose works**. Disponível em: <https://docs.docker.com/compose/intro/compose-application-model/>. Acesso em: 31 maio 2026.
- DOCKER. **Networking in Compose**. Disponível em: <https://docs.docker.com/compose/how-tos/networking/>. Acesso em: 31 maio 2026.
- DOCKER. **Control startup and shutdown order in Compose**. Disponível em: <https://docs.docker.com/compose/how-tos/startup-order/>. Acesso em: 31 maio 2026.
- DOCKER. **Define and manage volumes in Docker Compose**. Disponível em: <https://docs.docker.com/reference/compose-file/volumes/>. Acesso em: 31 maio 2026.
- DOCKER. **Environment variables in Compose**. Disponível em: <https://docs.docker.com/compose/how-tos/environment-variables/>. Acesso em: 31 maio 2026.
- DOCKER. **Set environment variables within your container's environment**. Disponível em: <https://docs.docker.com/compose/how-tos/environment-variables/set-environment-variables/>. Acesso em: 31 maio 2026.
- DOCKER. **Compose Deploy Specification**. Disponível em: <https://docs.docker.com/reference/compose-file/deploy/>. Acesso em: 31 maio 2026.
- DOCKER. **Compose Develop Specification**. Disponível em: <https://docs.docker.com/reference/compose-file/develop/>. Acesso em: 31 maio 2026.
- DOCKER. **Docker Compose Quickstart**. Disponível em: <https://docs.docker.com/compose/gettingstarted/>. Acesso em: 31 maio 2026.
- DOCKER. **Overview of installing Docker Compose**. Disponível em: <https://docs.docker.com/compose/install/>. Acesso em: 31 maio 2026.
