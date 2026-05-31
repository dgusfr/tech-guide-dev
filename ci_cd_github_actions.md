# CI/CD com GitHub Actions para Projetos Backend

## Sobre esta apostila

Esta apostila apresenta, de forma prática e didática, como criar pipelines de **CI/CD** para projetos backend usando **GitHub Actions**. O objetivo não é apenas mostrar exemplos de YAML, mas explicar o raciocínio por trás de cada etapa: quando a pipeline roda, o que ela valida, como ela protege a branch principal, como usar banco de dados nos testes, como integrar Docker e Docker Compose e como preparar uma imagem Docker para publicação.

O material foi reorganizado a partir de uma apostila inicial sobre CI/CD e expandido para servir como referência profissional para estudos e implementação em projetos reais de backend.

## Como estudar por esta apostila

Leia os capítulos na ordem, mas teste os exemplos em um repositório próprio. CI/CD é um assunto que fica muito mais claro quando você vê o workflow falhar, corrige o erro, faz um novo commit e acompanha o status check ficar verde no Pull Request.

Sempre que possível, rode localmente os mesmos comandos que a pipeline executa. A regra prática é: se o comando não funciona na sua máquina, dificilmente funcionará no runner do GitHub Actions.

## Índice

1. [Capítulo 1 — O que é CI/CD e por que isso importa](#capítulo-1--o-que-é-cicd-e-por-que-isso-importa)
2. [Capítulo 2 — Como o GitHub Actions funciona](#capítulo-2--como-o-github-actions-funciona)
3. [Capítulo 3 — Preparando o repositório para CI](#capítulo-3--preparando-o-repositório-para-ci)
4. [Capítulo 4 — Criando a primeira pipeline de CI](#capítulo-4--criando-a-primeira-pipeline-de-ci)
5. [Capítulo 5 — Pipelines para backend Python, Node.js e Go](#capítulo-5--pipelines-para-backend-python-nodejs-e-go)
6. [Capítulo 6 — Testes com banco de dados e service containers](#capítulo-6--testes-com-banco-de-dados-e-service-containers)
7. [Capítulo 7 — Usando Docker na pipeline](#capítulo-7--usando-docker-na-pipeline)
8. [Capítulo 8 — Usando Docker Compose na pipeline](#capítulo-8--usando-docker-compose-na-pipeline)
9. [Capítulo 9 — Build e publicação de imagem Docker](#capítulo-9--build-e-publicação-de-imagem-docker)
10. [Capítulo 10 — Branch protection, rulesets e Pull Requests](#capítulo-10--branch-protection-rulesets-e-pull-requests)
11. [Capítulo 11 — Secrets, variáveis e segurança](#capítulo-11--secrets-variáveis-e-segurança)
12. [Capítulo 12 — Entrega contínua e deploy controlado](#capítulo-12--entrega-contínua-e-deploy-controlado)
13. [Capítulo 13 — Pipeline completa de exemplo para backend](#capítulo-13--pipeline-completa-de-exemplo-para-backend)
14. [Capítulo 14 — Erros comuns e como resolver](#capítulo-14--erros-comuns-e-como-resolver)
15. [Capítulo 15 — Checklist profissional de CI/CD](#capítulo-15--checklist-profissional-de-cicd)
16. [Capítulo 16 — Cheat sheet de GitHub Actions e CI/CD](#capítulo-16--cheat-sheet-de-github-actions-e-cicd)
17. [Exercícios](#exercícios)
18. [Referências bibliográficas](#referências-bibliográficas)

---

# Capítulo 1 — O que é CI/CD e por que isso importa

Antes de falar de GitHub Actions, é importante entender o problema. Em um projeto backend, o código passa por várias etapas antes de chegar ao ambiente real: o desenvolvedor altera o código, cria testes, executa a aplicação localmente, abre um Pull Request, recebe revisão, faz merge, gera uma imagem Docker e, em algum momento, o sistema é implantado em um ambiente de homologação ou produção.

Quando esse fluxo é feito manualmente, muitos erros passam despercebidos. Um teste pode não ser executado, um arquivo `.env` pode faltar, uma migration pode quebrar, uma imagem Docker pode não subir ou uma alteração pode ser enviada direto para a `main` sem validação. CI/CD existe para reduzir esse risco.

![Fluxo geral de CI/CD](images/ci-cd/pipeline-ci-cd.svg)

Ao final deste capítulo, você será capaz de:

- explicar a diferença entre CI, Continuous Delivery e Continuous Deployment;
- entender por que uma pipeline protege o projeto;
- identificar quais etapas fazem sentido em uma aplicação backend;
- diferenciar validação de código, build, publicação e deploy.

## 1.1 — O problema

Imagine que você trabalha em uma API de pagamentos. Você cria uma branch, altera uma regra de validação, testa manualmente uma rota e abre um Pull Request. Outro desenvolvedor aprova, mas ninguém executou a suíte completa de testes. Depois do merge, um endpoint antigo quebra porque dependia da validação anterior.

Esse é exatamente o tipo de problema que uma pipeline de CI ajuda a evitar. A cada `push` ou Pull Request, o GitHub Actions executa automaticamente uma sequência de verificações. Se algo falhar, o Pull Request fica bloqueado até que o problema seja corrigido.

## 1.2 — O que é CI?

**CI**, ou **Continuous Integration**, significa **Integração Contínua**. Na prática, é a disciplina de integrar código frequentemente em um repositório compartilhado e validar automaticamente se essa integração não quebrou o projeto.

Em um projeto backend, uma pipeline de CI normalmente executa etapas como:

```text
checkout do código
instalação de dependências
lint e formatação
testes unitários
testes de integração
build da aplicação
build da imagem Docker
```

O objetivo da CI é responder uma pergunta simples: **este código pode ser integrado com segurança na base principal?**

## 1.3 — O que é CD?

**CD** pode significar duas práticas diferentes, dependendo do contexto.

A primeira é **Continuous Delivery**, ou **Entrega Contínua**. Nesse modelo, a pipeline prepara o software para ser implantado, gerando artefatos como pacotes, binários ou imagens Docker. O deploy em produção ainda pode depender de aprovação humana.

A segunda é **Continuous Deployment**, ou **Implantação Contínua**. Nesse modelo, além de preparar o software, a pipeline também faz o deploy automaticamente quando todas as validações passam.

A diferença prática é esta:

| Prática | O que acontece |
|---|---|
| CI | testa e valida o código |
| Continuous Delivery | prepara o artefato para deploy |
| Continuous Deployment | faz o deploy automaticamente |

Para um desenvolvedor backend júnior, o caminho mais seguro é começar com **CI bem feita**, depois adicionar **build de imagem Docker** e só então evoluir para deploy automatizado.

## 1.4 — O que uma boa pipeline backend precisa validar?

Uma pipeline backend profissional deve validar mais do que apenas “o teste passou”. Ela deve garantir que o projeto consegue ser instalado, analisado, testado, empacotado e executado em um ambiente limpo.

Um fluxo comum é:

```text
1. Baixar o código do repositório.
2. Preparar a linguagem usada no projeto.
3. Instalar dependências.
4. Executar lint e formatação.
5. Rodar testes unitários.
6. Subir banco/cache/fila para testes de integração.
7. Rodar testes de integração.
8. Fazer build da aplicação.
9. Fazer build da imagem Docker.
10. Opcionalmente publicar a imagem em um registry.
```

Essa sequência combina com o que já foi estudado nas apostilas de Docker e Docker Compose: a pipeline deve conseguir reproduzir, dentro do runner, o ambiente mínimo necessário para validar a aplicação.

## 1.5 — Resumo do capítulo

CI/CD automatiza validações que antes eram feitas manualmente. Em backend, isso é especialmente importante porque APIs dependem de testes, bancos de dados, variáveis de ambiente, migrations, imagens Docker e contratos entre serviços.

A CI responde se o código pode entrar na branch principal. A entrega contínua prepara o sistema para deploy. A implantação contínua coloca o sistema no ambiente final automaticamente.

---

# Capítulo 2 — Como o GitHub Actions funciona

O GitHub Actions é a ferramenta de automação integrada ao GitHub. Ele permite criar arquivos YAML dentro do repositório para definir o que deve acontecer quando certos eventos ocorrem, como um `push`, um Pull Request ou uma execução manual.

Ao final deste capítulo, você será capaz de:

- entender a estrutura de um workflow;
- diferenciar workflow, job, step e action;
- criar um arquivo básico dentro de `.github/workflows`;
- interpretar a execução de uma pipeline na aba **Actions** do GitHub.

## 2.1 — O problema

Um erro comum ao começar com GitHub Actions é copiar um arquivo YAML sem entender a estrutura. Isso funciona até o primeiro erro. Quando a pipeline falha, a pessoa não sabe se o problema está no gatilho, no runner, no job, no step, na action usada, na variável de ambiente ou no comando executado.

Por isso, primeiro precisamos entender a anatomia de um workflow.

![Anatomia de um workflow no GitHub Actions](images/ci-cd/github-actions-anatomy.svg)

## 2.2 — O que é um workflow?

Um **workflow** é um processo automatizado definido por um arquivo YAML. Ele deve ficar dentro da pasta:

```text
.github/workflows/
```

Exemplo:

```text
.github/workflows/ci.yml
```

Um repositório pode ter vários workflows, como:

```text
.github/workflows/ci.yml
.github/workflows/docker.yml
.github/workflows/deploy-staging.yml
.github/workflows/security.yml
```

Cada workflow pode ter um objetivo específico. Em projetos pequenos, é comum começar com apenas um arquivo `ci.yml`.

## 2.3 — O que é um evento?

O evento define **quando** o workflow será executado. Os eventos mais usados em backend são `push`, `pull_request` e `workflow_dispatch`.

Exemplo:

```yaml
on:
  push:
    branches: ["main"]
  pull_request:
    branches: ["main"]
  workflow_dispatch:
```

Esse bloco significa:

```text
Execute o workflow quando houver push na main,
quando houver Pull Request para a main,
ou quando alguém disparar manualmente pela interface do GitHub.
```

## 2.4 — O que é um runner?

O **runner** é a máquina onde os comandos da pipeline serão executados. Normalmente usamos runners hospedados pelo próprio GitHub, como:

```yaml
runs-on: ubuntu-latest
```

Isso significa que o job será executado em uma máquina Linux temporária. Ela nasce limpa, executa os passos da pipeline e depois é descartada. Por isso, você não deve assumir que a máquina já tem as dependências do seu projeto, arquivos locais, banco de dados ou variáveis de ambiente específicas.

## 2.5 — O que é um job?

Um **job** é um conjunto de passos executados no mesmo runner. Um workflow pode ter um ou vários jobs.

Exemplo:

```yaml
jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - name: Exibir versão do sistema
        run: uname -a
```

Nesse exemplo, o job se chama `ci`. Esse nome é importante porque ele aparece como status check no Pull Request. Em repositórios com branch protegida, é comum exigir que o job `ci` passe antes do merge.

## 2.6 — O que é um step?

Um **step** é uma etapa dentro de um job. Ele pode executar um comando com `run` ou usar uma action pronta com `uses`.

Exemplo usando `run`:

```yaml
- name: Rodar testes
  run: pytest
```

Exemplo usando `uses`:

```yaml
- name: Baixar código
  uses: actions/checkout@v4
```

A diferença é simples: `run` executa um comando de terminal. `uses` reaproveita uma action publicada, como `actions/checkout`, `actions/setup-python`, `actions/setup-node` ou actions oficiais do Docker.

## 2.7 — Primeiro workflow mínimo

Crie o arquivo:

```text
.github/workflows/ci.yml
```

Com o conteúdo:

```yaml
name: CI

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Exibir mensagem
        run: echo "Pipeline executada com sucesso"
```

Esse workflow ainda não testa a aplicação. Ele serve apenas para validar que o GitHub Actions está configurado corretamente.

## 2.8 — O que aconteceu no código?

O campo `name` define o nome que aparecerá na aba **Actions**. O bloco `on` define os eventos que disparam o workflow. O bloco `jobs` define o que será executado. O job `ci` roda em `ubuntu-latest`. O primeiro step baixa o código do repositório. O segundo step executa um comando simples no terminal.

Esse é o esqueleto de praticamente qualquer pipeline de CI.

## 2.9 — Resumo do capítulo

Um workflow é composto por eventos, jobs e steps. O GitHub Actions executa esses workflows em runners temporários. Para backend, a base é sempre a mesma: baixar código, preparar ambiente, instalar dependências, rodar verificações e reportar sucesso ou falha no Pull Request.

---

# Capítulo 3 — Preparando o repositório para CI

Antes de criar uma pipeline completa, o repositório precisa estar minimamente organizado. Uma pipeline ruim em um projeto desorganizado apenas automatiza a bagunça. Para que a CI funcione bem, o projeto precisa ter comandos claros para instalar dependências, rodar testes, executar lint e subir dependências externas quando necessário.

Ao final deste capítulo, você será capaz de:

- preparar a estrutura `.github/workflows`;
- definir comandos padronizados no projeto;
- entender a importância de rodar localmente antes de rodar no GitHub Actions;
- separar variáveis de ambiente locais, de teste e de produção.

## 3.1 — O problema

Uma pipeline precisa executar comandos previsíveis. Se cada desenvolvedor roda testes de um jeito, instala dependências de outro jeito e usa variáveis diferentes, a CI vira um ambiente frágil.

Antes de criar o workflow, defina comandos oficiais do projeto.

## 3.2 — Comandos oficiais do projeto

Em um backend Python, por exemplo, você pode definir comandos como:

```bash
pip install -r requirements.txt
ruff check .
pytest
```

Em um backend Node.js:

```bash
npm ci
npm run lint
npm test
npm run build
```

Em um backend Go:

```bash
go mod download
golangci-lint run
go test ./...
go build ./...
```

A pipeline deve usar esses comandos. Se eles falham localmente, corrija o projeto antes de culpar o GitHub Actions.

## 3.3 — Estrutura mínima recomendada

Uma estrutura simples para um projeto backend com CI pode ser:

```text
projeto-backend/
├── .github/
│   └── workflows/
│       └── ci.yml
├── src/
├── tests/
├── Dockerfile
├── docker-compose.yml
├── docker-compose.ci.yml
├── .env.example
├── requirements.txt
└── README.md
```

O arquivo `.env.example` não deve conter senhas reais. Ele serve para mostrar quais variáveis o projeto espera.

Exemplo:

```env
DATABASE_URL=postgresql://user:password@localhost:5432/app
REDIS_URL=redis://localhost:6379/0
ENVIRONMENT=development
```

## 3.4 — Quando usar `.env`, secrets e variables?

Em desenvolvimento local, usamos `.env`. No GitHub Actions, usamos **secrets** para dados sensíveis e **variables** para valores não sensíveis.

Use **secrets** para:

```text
senhas
tokens
chaves privadas
usuários de registry
credenciais de cloud
```

Use **variables** para:

```text
nome do ambiente
nome de imagem
flags não sensíveis
URLs públicas
```

Nunca coloque senhas reais dentro do arquivo YAML da pipeline.

## 3.5 — O papel do Docker e do Docker Compose

O Dockerfile define como empacotar a aplicação em uma imagem. O Docker Compose define como subir a aplicação junto com dependências, como banco de dados, cache e fila.

Na CI, você pode usar Docker de três formas:

| Uso | Quando faz sentido |
|---|---|
| Build da imagem Docker | validar que a aplicação pode ser empacotada |
| Service containers | subir dependências simples, como PostgreSQL ou Redis |
| Docker Compose | subir um ambiente multi-contêiner parecido com o ambiente local |

Para projetos backend pequenos, `services:` do GitHub Actions costuma ser suficiente. Para projetos com múltiplos serviços, Docker Compose é mais legível.

## 3.6 — Resumo do capítulo

Antes de automatizar, padronize. Uma boa pipeline depende de comandos claros, estrutura previsível, variáveis separadas e arquivos Docker bem definidos. A CI deve reproduzir o que o projeto precisa para ser validado em uma máquina limpa.

---

# Capítulo 4 — Criando a primeira pipeline de CI

Agora vamos transformar o esqueleto do GitHub Actions em uma pipeline real. A ideia é começar simples: baixar o código, instalar dependências, rodar lint e executar testes.

Ao final deste capítulo, você será capaz de:

- criar uma pipeline básica de CI;
- entender a ordem das etapas;
- configurar eventos para `push` e `pull_request`;
- interpretar falhas comuns.

## 4.1 — O problema

Sem CI, uma branch pode ser mesclada com erro de sintaxe, teste quebrado ou dependência incompatível. A pipeline básica resolve isso executando as verificações principais a cada Pull Request.

## 4.2 — Exemplo genérico

```yaml
name: CI

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Preparar ambiente
        run: echo "Aqui entra o setup da linguagem"

      - name: Instalar dependências
        run: echo "Aqui entra o comando de instalação"

      - name: Rodar lint
        run: echo "Aqui entra o lint"

      - name: Rodar testes
        run: echo "Aqui entram os testes"
```

Esse modelo é a base. A diferença entre Python, Node.js e Go está nos comandos e nas actions de setup.

## 4.3 — Ordem recomendada dos steps

A ordem mais comum é:

```text
checkout
setup da linguagem
cache de dependências
instalação de dependências
lint/formatação
testes
build
```

O lint normalmente vem antes dos testes porque ele falha rápido. Se o código nem passa em regras básicas de qualidade, não faz sentido gastar tempo executando testes mais pesados.

## 4.4 — Quando rodar a pipeline?

Para um projeto backend, uma configuração equilibrada é:

```yaml
on:
  pull_request:
    branches: ["main", "develop"]
  push:
    branches: ["main", "develop"]
  workflow_dispatch:
```

Isso roda a CI quando alguém abre PR para `main` ou `develop`, quando há push direto nessas branches e quando alguém executa manualmente.

Se quiser rodar em qualquer branch, use:

```yaml
on:
  push:
    branches: ["**"]
  pull_request:
    branches: ["**"]
```

Evite `branches: [ '*' ]` sem entender o contexto. Em YAML, o `*` tem significado especial para alias, por isso precisa estar entre aspas quando usado literalmente.

## 4.5 — Resumo do capítulo

A primeira pipeline deve ser simples. O objetivo é validar a automação antes de adicionar banco, Docker, Compose, build de imagem e deploy. Uma pipeline pequena e confiável é melhor do que uma pipeline enorme que ninguém entende.

---

# Capítulo 5 — Pipelines para backend Python, Node.js e Go

Este capítulo traz exemplos práticos para três stacks comuns em backend. Não é necessário decorar todos. O importante é entender o padrão: preparar a linguagem, instalar dependências, rodar lint, executar testes e gerar build quando aplicável.

Ao final deste capítulo, você será capaz de:

- adaptar uma pipeline para Python, Node.js ou Go;
- entender a diferença entre `pip install`, `npm ci` e `go test`;
- usar actions oficiais de setup;
- criar pipelines específicas para projetos backend.

## 5.1 — Pipeline para Python/FastAPI

Este exemplo considera um projeto Python com `requirements.txt`, `pytest` e `ruff`.

```yaml
name: CI Python

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: "pip"

      - name: Instalar dependências
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Rodar lint
        run: ruff check .

      - name: Rodar testes
        run: pytest -q
```

### O que aconteceu no código?

O `actions/setup-python` instala a versão do Python definida no workflow. O `cache: "pip"` ajuda a reutilizar dependências entre execuções. Depois, a pipeline instala dependências, roda `ruff` para qualidade estática e executa `pytest`.

## 5.2 — Pipeline para Node.js

Este exemplo considera um backend Node.js com scripts no `package.json`.

```yaml
name: CI Node

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Node.js
        uses: actions/setup-node@v4
        with:
          node-version: "22"
          cache: "npm"

      - name: Instalar dependências
        run: npm ci

      - name: Rodar lint
        run: npm run lint

      - name: Rodar testes
        run: npm test

      - name: Gerar build
        run: npm run build --if-present
```

### Por que usar `npm ci`?

O `npm ci` é mais adequado para CI porque instala dependências com base no `package-lock.json`, evitando variações inesperadas. Em pipeline, queremos reprodutibilidade, não instalação flexível.

## 5.3 — Pipeline para Go

Este exemplo considera um backend Go com testes em `go test ./...` e lint com `golangci-lint`.

```yaml
name: CI Go

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]

jobs:
  ci:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Go
        uses: actions/setup-go@v5
        with:
          go-version: "1.23"
          cache: true

      - name: Baixar dependências
        run: go mod download

      - name: Rodar lint
        uses: golangci/golangci-lint-action@v6
        with:
          version: latest

      - name: Rodar testes
        run: go test ./...

      - name: Gerar build
        run: go build ./...
```

### Por que usar action de lint em vez de `docker run -it`?

Em CI, comandos interativos costumam falhar porque o runner não fornece um terminal interativo da mesma forma que sua máquina local. Por isso, evite flags como `-it` dentro da pipeline. Para `golangci-lint`, a action oficial ou mantida pelo projeto é mais adequada do que rodar um contêiner manualmente com TTY.

## 5.4 — Matriz de versões

Uma matriz permite rodar o mesmo job em múltiplas versões de linguagem ou sistemas operacionais.

Exemplo com Go:

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}

    strategy:
      fail-fast: false
      matrix:
        os: ["ubuntu-latest", "ubuntu-22.04"]
        go-version: ["1.22", "1.23"]

    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-go@v5
        with:
          go-version: ${{ matrix.go-version }}
          cache: true

      - run: go test ./...
```

Isso gera combinações como:

```text
ubuntu-latest + Go 1.22
ubuntu-latest + Go 1.23
ubuntu-22.04 + Go 1.22
ubuntu-22.04 + Go 1.23
```

Use matriz quando você realmente precisa garantir compatibilidade. Não use matriz apenas para parecer sofisticado, pois cada combinação aumenta tempo e consumo da pipeline.

## 5.5 — Resumo do capítulo

A estrutura muda pouco entre linguagens. O padrão é sempre baixar código, configurar runtime, instalar dependências, validar qualidade, rodar testes e fazer build. Para backend, a diferença principal aparece quando os testes dependem de banco, cache, fila ou serviços externos.

---

# Capítulo 6 — Testes com banco de dados e service containers

Muitos backends não são apenas funções isoladas. Eles dependem de PostgreSQL, MySQL, Redis, MongoDB ou RabbitMQ. Para testar esse tipo de aplicação no GitHub Actions, você pode usar **service containers**, que são contêineres auxiliares executados junto com o job.

Ao final deste capítulo, você será capaz de:

- subir banco de dados dentro do GitHub Actions;
- configurar variáveis de ambiente para os testes;
- usar healthcheck para aguardar o serviço ficar pronto;
- diferenciar service containers de Docker Compose.

## 6.1 — O problema

Sua API funciona localmente porque você tem um PostgreSQL rodando no Docker Compose. Porém, quando a CI executa `pytest`, os testes falham porque não existe banco de dados no runner.

A solução é subir o banco na própria pipeline.

## 6.2 — Exemplo com PostgreSQL

```yaml
name: CI com PostgreSQL

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]

jobs:
  test:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_USER: app
          POSTGRES_PASSWORD: app
          POSTGRES_DB: app_test
        ports:
          - 5432:5432
        options: >-
          --health-cmd="pg_isready -U app -d app_test"
          --health-interval=10s
          --health-timeout=5s
          --health-retries=5

    env:
      DATABASE_URL: postgresql://app:app@localhost:5432/app_test
      ENVIRONMENT: test

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: "pip"

      - name: Instalar dependências
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Rodar migrations
        run: alembic upgrade head

      - name: Rodar testes
        run: pytest -q
```

## 6.3 — O que aconteceu no código?

O bloco `services` cria um contêiner PostgreSQL durante a execução do job. Como o job roda diretamente no runner, o banco fica acessível por `localhost:5432`. O `health-cmd` ajuda o GitHub Actions a verificar se o PostgreSQL está pronto antes de iniciar os steps que dependem dele.

A variável `DATABASE_URL` aponta os testes para o banco criado pela pipeline. Isso evita depender de banco local, banco compartilhado ou ambiente externo.

## 6.4 — Exemplo com Redis

```yaml
services:
  redis:
    image: redis:7
    ports:
      - 6379:6379
    options: >-
      --health-cmd="redis-cli ping"
      --health-interval=10s
      --health-timeout=5s
      --health-retries=5

env:
  REDIS_URL: redis://localhost:6379/0
```

Use isso quando sua aplicação precisa validar cache, filas simples, locks distribuídos ou rate limiting.

## 6.5 — Quando usar service containers e quando usar Docker Compose?

| Situação | Melhor opção |
|---|---|
| Testes precisam de apenas PostgreSQL | `services:` do GitHub Actions |
| Testes precisam de PostgreSQL e Redis | `services:` ainda funciona bem |
| Testes precisam da API, banco, fila e worker | Docker Compose |
| Você quer reproduzir exatamente o ambiente local | Docker Compose |
| Você quer uma pipeline mais simples e rápida | service containers |

## 6.6 — Resumo do capítulo

Service containers são ideais para dependências simples em testes de integração. Eles tornam a CI mais realista, porque os testes validam a aplicação contra serviços reais, não apenas mocks. Para ambientes mais complexos, Docker Compose costuma ser mais organizado.

---

# Capítulo 7 — Usando Docker na pipeline

Docker entra na pipeline para responder outra pergunta: **além de passar nos testes, minha aplicação consegue ser empacotada e executada como contêiner?**

Ao final deste capítulo, você será capaz de:

- fazer build de uma imagem Docker na CI;
- rodar um teste básico contra o contêiner;
- entender tags de imagem por commit SHA;
- evitar publicar imagens quebradas.

## 7.1 — O problema

Um projeto pode passar nos testes, mas ainda assim falhar ao construir a imagem Docker. Isso acontece quando o `Dockerfile` está desatualizado, copia arquivos errados, não instala dependências ou usa uma versão incompatível da linguagem.

Por isso, uma CI profissional deve validar o build da imagem.

## 7.2 — Build simples da imagem

```yaml
- name: Build da imagem Docker
  run: docker build -t minha-api:${{ github.sha }} .
```

A tag `${{ github.sha }}` usa o hash do commit atual. Isso facilita rastrear qual versão do código gerou aquela imagem.

## 7.3 — Smoke test do contêiner

Depois do build, você pode executar um teste simples para verificar se a imagem sobe.

```yaml
- name: Executar contêiner para smoke test
  run: |
    docker run -d \
      --name api-smoke \
      -p 8000:8000 \
      minha-api:${{ github.sha }}

    sleep 5
    curl --fail http://localhost:8000/health

- name: Exibir logs em caso de falha
  if: failure()
  run: docker logs api-smoke || true

- name: Remover contêiner
  if: always()
  run: docker rm -f api-smoke || true
```

## 7.4 — O que é um smoke test?

Um smoke test é uma verificação rápida para saber se a aplicação pelo menos sobe e responde. Ele não substitui testes unitários ou de integração. Ele apenas valida que a imagem Docker não está completamente quebrada.

Exemplo de rota comum para isso:

```http
GET /health
```

Resposta esperada:

```json
{
  "status": "ok"
}
```

## 7.5 — Cuidados com Docker na CI

Não coloque segredos dentro da imagem. Não copie `.env` real para dentro do build. Não publique imagem se os testes falharem. Não use tag `latest` como única referência. Prefira tags com SHA, versão ou data.

Exemplo recomendado:

```text
minha-api:main
minha-api:sha-a1b2c3d
minha-api:v1.2.0
```

## 7.6 — Resumo do capítulo

Docker na CI valida se o projeto pode ser empacotado e executado como contêiner. O ideal é fazer build da imagem, subir o contêiner e executar pelo menos um smoke test antes de publicar ou implantar.

---

# Capítulo 8 — Usando Docker Compose na pipeline

Docker Compose é útil quando a aplicação precisa subir junto com banco, cache, filas, workers ou outros serviços. Ele permite declarar o ambiente em um arquivo YAML e reaproveitar essa mesma definição no desenvolvimento local e na CI.

Ao final deste capítulo, você será capaz de:

- usar Docker Compose no GitHub Actions;
- criar um arquivo `docker-compose.ci.yml`;
- rodar testes dentro de um serviço;
- capturar logs quando algo falhar;
- derrubar o ambiente corretamente.

## 8.1 — O problema

No desenvolvimento local, você executa:

```bash
docker compose up -d
```

Isso sobe API, PostgreSQL, Redis e talvez um worker. Mas na CI você roda apenas `pytest`, e os testes falham porque o ambiente real não existe.

A solução é usar Compose também na pipeline.

![Docker Compose na pipeline de CI](images/ci-cd/docker-compose-ci.svg)

## 8.2 — Exemplo de `docker-compose.ci.yml`

```yaml
services:
  api:
    build: .
    environment:
      DATABASE_URL: postgresql://app:app@postgres:5432/app_test
      REDIS_URL: redis://redis:6379/0
      ENVIRONMENT: test
    depends_on:
      postgres:
        condition: service_healthy
      redis:
        condition: service_healthy

  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: app
      POSTGRES_DB: app_test
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U app -d app_test"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
```

## 8.3 — Workflow usando Compose

```yaml
name: CI com Docker Compose

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]

jobs:
  compose-tests:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Subir ambiente de teste
        run: docker compose -f docker-compose.ci.yml up -d --build

      - name: Rodar migrations
        run: docker compose -f docker-compose.ci.yml exec -T api alembic upgrade head

      - name: Rodar testes
        run: docker compose -f docker-compose.ci.yml exec -T api pytest -q

      - name: Exibir logs em caso de falha
        if: failure()
        run: docker compose -f docker-compose.ci.yml logs

      - name: Derrubar ambiente
        if: always()
        run: docker compose -f docker-compose.ci.yml down -v --remove-orphans
```

## 8.4 — Por que usar `exec -T`?

Em CI, não existe um terminal interativo como no seu computador. A flag `-T` desativa a alocação de TTY. Isso evita erros como:

```text
the input device is not a TTY
```

Esse erro é comum quando usamos comandos copiados do ambiente local com flags interativas.

## 8.5 — Por que usar `down -v`?

O comando:

```bash
docker compose down -v --remove-orphans
```

remove contêineres, redes e volumes criados para o teste. Isso evita que uma execução contamine outra. Em CI, cada execução deve começar limpa.

## 8.6 — Compose de desenvolvimento vs Compose de CI

Evite usar exatamente o mesmo Compose de desenvolvimento se ele contém volumes de código, portas expostas desnecessárias ou serviços manuais. Uma boa prática é ter um arquivo base e um override para CI.

Exemplo:

```bash
docker compose -f docker-compose.yml -f docker-compose.ci.yml up -d --build
```

O primeiro arquivo define a aplicação. O segundo ajusta configurações específicas da CI, como variáveis de teste, comandos e healthchecks.

## 8.7 — Resumo do capítulo

Docker Compose permite validar a aplicação em um ambiente mais próximo do real. Em pipelines backend, ele é especialmente útil para testes de integração envolvendo API, banco, cache, filas e workers. Use `exec -T`, `logs` em caso de falha e `down -v` no final.

---

# Capítulo 9 — Build e publicação de imagem Docker

Depois que a CI passa, podemos gerar uma imagem Docker e publicá-la em um registry. Essa etapa faz parte da entrega contínua: o sistema ainda não precisa ser implantado automaticamente, mas já fica empacotado e pronto para deploy.

Ao final deste capítulo, você será capaz de:

- entender o papel de um registry;
- publicar imagens no Docker Hub ou GitHub Container Registry;
- usar tags seguras;
- separar build em Pull Request de publicação na `main`.

## 9.1 — O problema

Se cada pessoa gera imagem manualmente na própria máquina, não há garantia de rastreabilidade. Pode ser que a imagem publicada não corresponda exatamente ao código da `main`. Em CI/CD profissional, a própria pipeline gera a imagem a partir do commit validado.

## 9.2 — Publicar apenas depois dos testes

A regra é simples: **não publique imagem se a CI falhou**.

Um fluxo recomendado é:

```text
PR: roda testes e build da imagem, mas não publica.
main: roda testes, builda imagem e publica no registry.
```

## 9.3 — Exemplo com Docker Buildx e GitHub Container Registry

```yaml
name: Docker Image

on:
  push:
    branches: ["main"]

permissions:
  contents: read
  packages: write

jobs:
  docker:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login no GitHub Container Registry
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Metadados da imagem
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/${{ github.repository }}
          tags: |
            type=sha
            type=ref,event=branch

      - name: Build e push da imagem
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```

## 9.4 — O que aconteceu no código?

O `docker/setup-buildx-action` prepara o ambiente de build. O `docker/login-action` autentica no registry. O `docker/metadata-action` gera tags automaticamente com base no branch e no SHA. O `docker/build-push-action` faz o build e publica a imagem.

A permissão `packages: write` é necessária para publicar no GitHub Container Registry usando o `GITHUB_TOKEN`.

## 9.5 — Exemplo publicando no Docker Hub

Para Docker Hub, crie secrets no repositório:

```text
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

Workflow:

```yaml
- name: Login no Docker Hub
  uses: docker/login-action@v3
  with:
    username: ${{ secrets.DOCKERHUB_USERNAME }}
    password: ${{ secrets.DOCKERHUB_TOKEN }}

- name: Build e push
  uses: docker/build-push-action@v6
  with:
    context: .
    push: true
    tags: ${{ secrets.DOCKERHUB_USERNAME }}/minha-api:${{ github.sha }}
```

## 9.6 — Cuidados com `latest`

A tag `latest` não significa “mais estável”. Ela significa apenas uma tag chamada `latest`. Use com cuidado.

Um padrão melhor é publicar múltiplas tags:

```text
minha-api:sha-a1b2c3d
minha-api:main
minha-api:v1.0.0
```

Assim você consegue rastrear qual commit gerou cada imagem.

## 9.7 — Resumo do capítulo

Publicar imagens Docker pela pipeline aumenta rastreabilidade e segurança. A imagem deve ser gerada a partir do código que passou na CI. Pull Requests podem validar o build, mas a publicação deve ocorrer apenas em branches confiáveis, como `main`, ou em tags de release.

---

# Capítulo 10 — Branch protection, rulesets e Pull Requests

A CI só protege o projeto de verdade se a branch principal exigir que os status checks passem antes do merge. Caso contrário, alguém ainda pode ignorar a pipeline e fazer merge mesmo com erro.

Ao final deste capítulo, você será capaz de:

- configurar proteção para a branch `main`;
- exigir Pull Request antes do merge;
- exigir status checks;
- entender por que o nome do job importa.

## 10.1 — O problema

Você criou uma pipeline, mas um desenvolvedor ainda consegue fazer push direto na `main`. Nesse caso, a CI apenas avisa que quebrou, mas não impede a entrada do problema.

Para evitar isso, configure proteção de branch ou rulesets.

## 10.2 — Fluxo recomendado

```text
main protegida
feature branch criada
commits na feature
push da feature
Pull Request aberto
CI executa
review é feito
merge liberado somente se checks passarem
```

## 10.3 — Criando uma branch de trabalho

```bash
git switch main
git pull origin main
git switch -c feature/adicionar-ci
```

Faça alterações, commit e push:

```bash
git status
git add .
git commit -m "ci: add backend pipeline"
git push -u origin feature/adicionar-ci
```

Depois, abra um Pull Request no GitHub.

## 10.4 — Configurando proteção no GitHub

Na interface do GitHub, acesse:

```text
Repository → Settings → Rules → Rulesets
```

Ou, em alguns repositórios:

```text
Repository → Settings → Branches → Branch protection rules
```

Configure regras como:

```text
Require a pull request before merging
Require status checks to pass before merging
Require branches to be up to date before merging
Block force pushes
Block deletions
```

As imagens abaixo vieram do material original e mostram telas de configuração de secrets, rulesets e status checks no GitHub.

![Tela de secrets do GitHub Actions](images/ci-cd/screenshot-01.png)

![Configuração de branch ruleset](images/ci-cd/screenshot-05.png)

![Configuração de status checks e Pull Request obrigatório](images/ci-cd/screenshot-02.png)

## 10.5 — Atenção ao nome do job

Se o job se chama `ci`, o status check exibido no Pull Request também será `ci`. Se você configurar a branch para exigir o check `ci`, mantenha esse nome estável.

Exemplo:

```yaml
jobs:
  ci:
    runs-on: ubuntu-latest
```

Evite renomear jobs frequentemente em repositórios com regras de proteção, pois isso pode bloquear merges até que o novo status check seja reconhecido.

## 10.6 — Resumo do capítulo

Uma pipeline só vira barreira de qualidade quando está conectada à proteção da branch. O Pull Request deve ser o caminho oficial de entrada na `main`, e os status checks devem ser obrigatórios.

---

# Capítulo 11 — Secrets, variáveis e segurança

Pipelines lidam com tokens, senhas de banco, credenciais de Docker Hub, chaves de cloud e outros dados sensíveis. Se esses valores forem expostos, o risco é alto. Por isso, GitHub Actions possui mecanismos próprios para secrets e variables.

Ao final deste capítulo, você será capaz de:

- diferenciar secrets, variables e env;
- configurar secrets no GitHub;
- usar permissões mínimas;
- evitar vazamento de credenciais nos logs.

## 11.1 — O problema

Um erro comum é colocar credenciais diretamente no arquivo YAML:

```yaml
env:
  DB_PASSWORD: minha-senha-real
```

Isso é perigoso porque o arquivo fica versionado no Git. Mesmo que você remova depois, a senha pode continuar no histórico.

## 11.2 — Usando secrets

Crie secrets em:

```text
Repository → Settings → Secrets and variables → Actions → New repository secret
```

Exemplos:

```text
DB_HOST
DB_USER
DB_PASSWORD
DB_NAME
DB_PORT
DOCKERHUB_USERNAME
DOCKERHUB_TOKEN
```

Use no workflow assim:

```yaml
env:
  DB_HOST: ${{ secrets.DB_HOST }}
  DB_USER: ${{ secrets.DB_USER }}
  DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
  DB_NAME: ${{ secrets.DB_NAME }}
  DB_PORT: ${{ secrets.DB_PORT }}
```

## 11.3 — Secrets vs variables

| Tipo | Uso |
|---|---|
| `secrets` | dados sensíveis, como senhas e tokens |
| `vars` | configurações não sensíveis |
| `env` | variáveis usadas durante a execução do job |

Exemplo com `vars`:

```yaml
env:
  IMAGE_NAME: ${{ vars.IMAGE_NAME }}
  ENVIRONMENT: staging
```

## 11.4 — Permissões mínimas

Defina permissões do `GITHUB_TOKEN` conforme a necessidade do workflow.

Exemplo para CI simples:

```yaml
permissions:
  contents: read
```

Exemplo para publicar pacote no GitHub Container Registry:

```yaml
permissions:
  contents: read
  packages: write
```

Não dê permissão ampla se o job não precisa dela.

## 11.5 — Cuidados com Pull Requests de forks

Em projetos open source, Pull Requests vindos de forks têm restrições de acesso a secrets. Isso é uma proteção. Não tente contornar expondo secrets no código.

Para pipelines com secrets sensíveis, limite a execução a branches confiáveis ou use ambientes com aprovação.

## 11.6 — Boas práticas de segurança

- Não faça `echo` de secrets.
- Não grave `.env` com senha real no repositório.
- Não use tokens pessoais quando `GITHUB_TOKEN` é suficiente.
- Dê permissões mínimas ao workflow.
- Prefira actions oficiais ou amplamente mantidas.
- Mantenha actions atualizadas.
- Use branch protection.
- Exija review antes do merge.

## 11.7 — Resumo do capítulo

Secrets protegem dados sensíveis, mas não corrigem workflows inseguros. O ideal é combinar secrets, permissões mínimas, proteção de branch e boas práticas de revisão.

---

# Capítulo 12 — Entrega contínua e deploy controlado

Depois que a CI está estável, podemos pensar em CD. O primeiro passo normalmente é gerar artefatos confiáveis. O segundo é fazer deploy em ambiente de homologação. O terceiro, mais avançado, é automatizar produção com aprovações e ambientes protegidos.

Ao final deste capítulo, você será capaz de:

- diferenciar artefato, release e deploy;
- entender ambientes no GitHub Actions;
- criar um job de deploy condicionado à branch `main`;
- evitar deploy automático inseguro.

## 12.1 — O problema

Se qualquer push em qualquer branch faz deploy, uma alteração experimental pode ir para produção por acidente. CD precisa de regras claras.

## 12.2 — Deploy apenas na main

```yaml
deploy-staging:
  needs: ci
  runs-on: ubuntu-latest
  if: github.ref == 'refs/heads/main'

  steps:
    - name: Deploy em staging
      run: echo "Aqui entraria o deploy em staging"
```

O `needs: ci` garante que o deploy só roda depois da CI. O `if` restringe a execução à branch `main`.

## 12.3 — Usando environments

GitHub Actions permite associar jobs a ambientes, como:

```yaml
environment: staging
```

Ou:

```yaml
environment: production
```

Em ambientes de produção, você pode configurar aprovação manual antes do job continuar.

Exemplo:

```yaml
deploy-production:
  needs: docker
  runs-on: ubuntu-latest
  if: startsWith(github.ref, 'refs/tags/v')
  environment: production

  steps:
    - name: Deploy em produção
      run: echo "Deploy aprovado e executado"
```

## 12.4 — Delivery antes de deployment

Para estudo e projetos pessoais, um bom primeiro objetivo é fazer **Continuous Delivery**:

```text
CI passa
imagem Docker é criada
imagem é publicada no registry
deploy ainda é manual
```

Isso já é um grande avanço, porque o deploy deixa de depender de build local.

## 12.5 — Resumo do capítulo

CD deve ser implementado com cuidado. Comece gerando artefatos e imagens confiáveis. Depois adicione deploy em staging. Para produção, use ambientes, aprovações e regras específicas por branch ou tag.

---

# Capítulo 13 — Pipeline completa de exemplo para backend

Este capítulo reúne uma pipeline mais completa para um backend Python/FastAPI com PostgreSQL, lint, testes, Docker build e publicação de imagem apenas na `main`.

A ideia não é copiar sem pensar, mas usar como base para adaptar ao seu projeto.

## 13.1 — Arquivo `.github/workflows/ci.yml`

```yaml
name: CI Backend

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]
  workflow_dispatch:

permissions:
  contents: read

jobs:
  ci:
    name: ci
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_USER: app
          POSTGRES_PASSWORD: app
          POSTGRES_DB: app_test
        ports:
          - 5432:5432
        options: >-
          --health-cmd="pg_isready -U app -d app_test"
          --health-interval=10s
          --health-timeout=5s
          --health-retries=5

    env:
      DATABASE_URL: postgresql://app:app@localhost:5432/app_test
      ENVIRONMENT: test

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"
          cache: "pip"

      - name: Instalar dependências
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt

      - name: Validar formatação e lint
        run: ruff check .

      - name: Rodar migrations
        run: alembic upgrade head

      - name: Rodar testes
        run: pytest -q

      - name: Build da imagem Docker
        run: docker build -t minha-api:${{ github.sha }} .

      - name: Smoke test da imagem
        run: |
          docker run -d --name api-smoke -p 8000:8000 \
            -e DATABASE_URL=${DATABASE_URL} \
            -e ENVIRONMENT=test \
            minha-api:${{ github.sha }}

          sleep 5
          curl --fail http://localhost:8000/health

      - name: Logs do contêiner em caso de falha
        if: failure()
        run: docker logs api-smoke || true

      - name: Remover contêiner de smoke test
        if: always()
        run: docker rm -f api-smoke || true

  docker-publish:
    name: docker-publish
    needs: ci
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'

    permissions:
      contents: read
      packages: write

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Configurar Docker Buildx
        uses: docker/setup-buildx-action@v3

      - name: Login no GHCR
        uses: docker/login-action@v3
        with:
          registry: ghcr.io
          username: ${{ github.actor }}
          password: ${{ secrets.GITHUB_TOKEN }}

      - name: Gerar metadados da imagem
        id: meta
        uses: docker/metadata-action@v5
        with:
          images: ghcr.io/${{ github.repository }}
          tags: |
            type=sha
            type=ref,event=branch

      - name: Build e push
        uses: docker/build-push-action@v6
        with:
          context: .
          push: true
          tags: ${{ steps.meta.outputs.tags }}
          labels: ${{ steps.meta.outputs.labels }}
```

## 13.2 — O que adaptar nesse exemplo?

Você provavelmente precisará adaptar:

```text
python-version
requirements.txt
comando de lint
comando de migrations
comando de testes
porta da aplicação
rota /health
nome da imagem
variáveis de ambiente
```

## 13.3 — Versão com Docker Compose

Se seu projeto usa Compose para testes, o job `ci` pode ficar assim:

```yaml
jobs:
  ci:
    name: ci
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do código
        uses: actions/checkout@v4

      - name: Subir ambiente
        run: docker compose -f docker-compose.ci.yml up -d --build

      - name: Rodar migrations
        run: docker compose -f docker-compose.ci.yml exec -T api alembic upgrade head

      - name: Rodar testes
        run: docker compose -f docker-compose.ci.yml exec -T api pytest -q

      - name: Logs em caso de falha
        if: failure()
        run: docker compose -f docker-compose.ci.yml logs

      - name: Derrubar ambiente
        if: always()
        run: docker compose -f docker-compose.ci.yml down -v --remove-orphans
```

## 13.4 — Qual versão escolher?

Use service containers quando o ambiente é simples. Use Compose quando você precisa reproduzir um ambiente multi-contêiner com API, banco, cache, filas e workers.

---

# Capítulo 14 — Erros comuns e como resolver

Toda pipeline falha. Isso é normal. O importante é saber interpretar o erro e corrigir de forma objetiva.

## 14.1 — Erro: `the input device is not a TTY`

Causa provável: comando Docker usando `-it` dentro do GitHub Actions.

Errado:

```bash
docker run --rm -it minha-imagem comando
```

Certo:

```bash
docker run --rm minha-imagem comando
```

Com Docker Compose, use:

```bash
docker compose exec -T api pytest
```

## 14.2 — Erro: banco não está pronto

Causa provável: a aplicação tenta conectar antes do banco aceitar conexões.

Soluções:

```text
adicionar healthcheck
usar depends_on com condition em Compose
usar retry na aplicação
aumentar robustez dos testes
```

## 14.3 — Erro: segredo aparece vazio

Causas possíveis:

```text
secret não foi criado no repositório
nome do secret está diferente
workflow roda em Pull Request de fork
job não tem acesso ao environment correto
```

## 14.4 — Erro: status check não aparece na branch protection

O GitHub pode exigir que o workflow rode pelo menos uma vez antes de permitir selecionar o status check. Faça um Pull Request, deixe o job aparecer e depois configure a regra.

## 14.5 — Erro: build usa `${{ matrix.os }}` mas não existe matriz

Se um job usa `matrix.os`, ele precisa declarar a strategy:

```yaml
strategy:
  matrix:
    os: ["ubuntu-latest", "ubuntu-22.04"]

runs-on: ${{ matrix.os }}
```

## 14.6 — Erro: Docker Compose não encontra arquivo

Verifique se o arquivo está no repositório e se o comando roda na raiz correta.

```bash
ls -la
docker compose -f docker-compose.ci.yml config
```

O comando `config` é excelente para validar se o Compose está sintaticamente correto.

## 14.7 — Erro: testes passam localmente, mas falham na CI

Possíveis causas:

```text
variáveis de ambiente diferentes
dependências não fixadas
banco local tem dados que a CI não tem
falta migration
caminho de arquivo relativo incorreto
teste depende de ordem ou horário
```

A solução é tornar os testes mais determinísticos.

---

# Capítulo 15 — Checklist profissional de CI/CD

Use este checklist antes de considerar sua pipeline pronta.

## 15.1 — Checklist de CI

- [ ] O workflow está em `.github/workflows/`.
- [ ] A pipeline roda em Pull Requests para `main`.
- [ ] A pipeline roda em push na `main`.
- [ ] O código é baixado com `actions/checkout`.
- [ ] A versão da linguagem é definida explicitamente.
- [ ] Dependências são instaladas de forma reprodutível.
- [ ] O lint roda na pipeline.
- [ ] Os testes unitários rodam na pipeline.
- [ ] Testes de integração sobem banco/cache quando necessário.
- [ ] O build da aplicação roda.
- [ ] O build da imagem Docker roda.
- [ ] Logs são exibidos quando testes com Compose falham.

## 15.2 — Checklist de branch protection

- [ ] Push direto na `main` está bloqueado.
- [ ] Pull Request é obrigatório.
- [ ] Status check `ci` é obrigatório.
- [ ] Branch precisa estar atualizada antes do merge.
- [ ] Force push está bloqueado.
- [ ] Delete branch protegida está bloqueado.

## 15.3 — Checklist de segurança

- [ ] Nenhum segredo está hardcoded no YAML.
- [ ] Secrets estão cadastrados no GitHub.
- [ ] `.env` real não está versionado.
- [ ] Existe `.env.example`.
- [ ] `permissions` usa mínimo necessário.
- [ ] Deploy em produção exige ambiente protegido ou regra específica.
- [ ] Imagens Docker não recebem secrets no build.

## 15.4 — Checklist de Docker e Compose

- [ ] O `Dockerfile` constrói sem depender de arquivos locais ignorados.
- [ ] Existe `.dockerignore`.
- [ ] A imagem sobe localmente.
- [ ] A aplicação tem rota `/health` ou equivalente.
- [ ] O Compose de CI usa healthchecks.
- [ ] A pipeline executa `docker compose down -v` no final.
- [ ] Comandos de CI não usam `-it`.

---

# Capítulo 16 — Cheat sheet de GitHub Actions e CI/CD

## 16.1 — Estrutura básica

```yaml
name: CI

on:
  pull_request:
    branches: ["main"]
  push:
    branches: ["main"]

jobs:
  ci:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: echo "Hello CI"
```

## 16.2 — Eventos comuns

```yaml
on:
  push:
  pull_request:
  workflow_dispatch:
```

```yaml
on:
  push:
    branches: ["main"]
```

```yaml
on:
  pull_request:
    branches: ["main", "develop"]
```

## 16.3 — Condições úteis

```yaml
if: github.ref == 'refs/heads/main'
```

```yaml
if: startsWith(github.ref, 'refs/tags/v')
```

```yaml
if: failure()
```

```yaml
if: always()
```

## 16.4 — Python

```yaml
- uses: actions/setup-python@v5
  with:
    python-version: "3.12"
    cache: "pip"

- run: pip install -r requirements.txt
- run: ruff check .
- run: pytest -q
```

## 16.5 — Node.js

```yaml
- uses: actions/setup-node@v4
  with:
    node-version: "22"
    cache: "npm"

- run: npm ci
- run: npm run lint
- run: npm test
- run: npm run build --if-present
```

## 16.6 — Go

```yaml
- uses: actions/setup-go@v5
  with:
    go-version: "1.23"
    cache: true

- run: go mod download
- uses: golangci/golangci-lint-action@v6
- run: go test ./...
- run: go build ./...
```

## 16.7 — PostgreSQL service container

```yaml
services:
  postgres:
    image: postgres:16
    env:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: app
      POSTGRES_DB: app_test
    ports:
      - 5432:5432
    options: >-
      --health-cmd="pg_isready -U app -d app_test"
      --health-interval=10s
      --health-timeout=5s
      --health-retries=5
```

## 16.8 — Redis service container

```yaml
services:
  redis:
    image: redis:7
    ports:
      - 6379:6379
    options: >-
      --health-cmd="redis-cli ping"
      --health-interval=10s
      --health-timeout=5s
      --health-retries=5
```

## 16.9 — Docker build

```yaml
- run: docker build -t minha-api:${{ github.sha }} .
```

## 16.10 — Smoke test com Docker

```yaml
- run: |
    docker run -d --name api -p 8000:8000 minha-api:${{ github.sha }}
    sleep 5
    curl --fail http://localhost:8000/health
```

## 16.11 — Docker Compose na CI

```yaml
- run: docker compose -f docker-compose.ci.yml up -d --build
- run: docker compose -f docker-compose.ci.yml exec -T api pytest -q
- if: failure()
  run: docker compose -f docker-compose.ci.yml logs
- if: always()
  run: docker compose -f docker-compose.ci.yml down -v --remove-orphans
```

## 16.12 — Publicar imagem no GHCR

```yaml
permissions:
  contents: read
  packages: write
```

```yaml
- uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}

- uses: docker/build-push-action@v6
  with:
    context: .
    push: true
    tags: ghcr.io/${{ github.repository }}:${{ github.sha }}
```

## 16.13 — Secrets

```yaml
env:
  DB_USER: ${{ secrets.DB_USER }}
  DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

## 16.14 — Matrix

```yaml
strategy:
  fail-fast: false
  matrix:
    os: ["ubuntu-latest", "ubuntu-22.04"]
    python-version: ["3.11", "3.12"]

runs-on: ${{ matrix.os }}
```

## 16.15 — Branch protection essencial

```text
Settings → Rules → Rulesets
Target: main
Require pull request before merging
Require status checks to pass
Required check: ci
Block force pushes
```

---

# Exercícios

## Exercícios teóricos

1. Explique a diferença entre Continuous Integration, Continuous Delivery e Continuous Deployment.
2. O que é um runner no GitHub Actions?
3. Qual é a diferença entre `run` e `uses` em um step?
4. Por que uma pipeline deve rodar em Pull Requests?
5. Por que não devemos colocar senhas diretamente no arquivo YAML?
6. Qual é a diferença entre service containers e Docker Compose em uma pipeline?
7. Por que `docker compose exec -T` é mais adequado para CI do que `docker compose exec -it`?
8. O que significa exigir status checks antes do merge?

## Exercícios práticos

1. Crie um workflow mínimo que rode em Pull Requests para `main`.
2. Adicione um job chamado `ci` que execute um comando simples.
3. Configure uma pipeline para seu projeto backend rodar lint e testes.
4. Adicione PostgreSQL como service container.
5. Crie uma rota `/health` na sua API e faça um smoke test com Docker.
6. Crie um `docker-compose.ci.yml` e rode seus testes por Compose.
7. Configure um ruleset exigindo que o job `ci` passe antes do merge.
8. Publique uma imagem Docker no GHCR apenas quando houver push na `main`.

## Desafios

1. Adicione uma matriz para testar duas versões da linguagem usada no seu projeto.
2. Separe sua pipeline em dois jobs: `test` e `docker-build`.
3. Faça o job `docker-build` depender de `test` com `needs`.
4. Adicione upload de relatório de testes como artifact.
5. Configure um ambiente `production` com aprovação manual antes do deploy.

---

# Referências bibliográficas

- GITHUB DOCS. **Workflow syntax for GitHub Actions**. Disponível em: <https://docs.github.com/actions/using-workflows/workflow-syntax-for-github-actions>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Events that trigger workflows**. Disponível em: <https://docs.github.com/actions/using-workflows/events-that-trigger-workflows>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Using secrets in GitHub Actions**. Disponível em: <https://docs.github.com/actions/security-guides/using-secrets-in-github-actions>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Communicating with Docker service containers**. Disponível em: <https://docs.github.com/actions/tutorials/communicating-with-docker-service-containers>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Creating PostgreSQL service containers**. Disponível em: <https://docs.github.com/actions/guides/creating-postgresql-service-containers>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Managing a branch protection rule**. Disponível em: <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-protected-branches/managing-a-branch-protection-rule>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Available rules for rulesets**. Disponível em: <https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets/available-rules-for-rulesets>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Use GITHUB_TOKEN for authentication in workflows**. Disponível em: <https://docs.github.com/actions/reference/authentication-in-a-workflow>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Secure use reference**. Disponível em: <https://docs.github.com/en/actions/reference/security/secure-use>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **Managing environments for deployment**. Disponível em: <https://docs.github.com/actions/deployment/targeting-different-environments/using-environments-for-deployment>. Acesso em: 31 maio 2026.
- DOCKER DOCS. **Get started with GitHub Actions**. Disponível em: <https://docs.docker.com/build/ci/github-actions/>. Acesso em: 31 maio 2026.
- DOCKER DOCS. **Introduction to GitHub Actions with Docker**. Disponível em: <https://docs.docker.com/guides/gha/>. Acesso em: 31 maio 2026.
- DOCKER DOCS. **Compose file reference**. Disponível em: <https://docs.docker.com/reference/compose-file/>. Acesso em: 31 maio 2026.
- DOCKER DOCS. **Define services in Docker Compose**. Disponível em: <https://docs.docker.com/reference/compose-file/services/>. Acesso em: 31 maio 2026.
- DOCKER DOCS. **Control startup and shutdown order in Compose**. Disponível em: <https://docs.docker.com/compose/how-tos/startup-order/>. Acesso em: 31 maio 2026.
- DOCKER DOCS. **Use multiple Compose files**. Disponível em: <https://docs.docker.com/compose/how-tos/multiple-compose-files/>. Acesso em: 31 maio 2026.
