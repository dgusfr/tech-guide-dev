# 📚 Tech Guide Desenvolvimento

Bem-vindo ao **Tech Guide**! Este repositório reúne apostilas didáticas, organizadas e práticas sobre os principais tópicos de desenvolvimento backend, infraestrutura, arquitetura de sistemas e boas práticas de programação.

Cada apostila foi cuidadosamente estruturada para ser estudada progressivamente, com explicações em português, exemplos práticos e muito foco em **entender o "por quê"** de cada conceito, não apenas memorizar como fazer.

---

## 🎯 Como usar este guia

1. **Escolha um tópico** de interesse na lista abaixo
2. **Leia os capítulos em ordem** — geralmente começam pelos fundamentos
3. **Execute os exemplos** em seu ambiente local
4. **Pratique** alterando o código e observando os resultados
5. **Consulte** quando precisar revisar um conceito

---

## 📖 Índice de Apostilas

### 🐍 Linguagens de Programação

#### [Python](python.md)
**Apostila Didática de Python** — Fundamentos da linguagem Python com progressão clara do básico ao avançado.
- Sintaxe básica, variáveis e tipos de dados
- Strings, listas e estruturas de dados
- Condicionais e operadores
- Estruturas de controle (`match/case`)
- Funções, escopo e boas práticas

> **Público:** Iniciantes que desejam aprender Python ou consolidar os fundamentos.

---

#### [Programação Orientada a Objetos com Python](poo_python.md)
**POO em Python** — Conceitos de orientação a objetos de forma progressiva e prática.
- Classes, objetos, atributos e métodos
- Construtores e `self`
- Encapsulamento e controle de acesso
- Herança, polimorfismo e composição
- Métodos especiais e dataclasses
- Boas práticas de modelagem

> **Público:** Programadores Python que desejam dominar orientação a objetos.

---

#### [Estruturas de Dados em Python](estruturas_dados_python.md)
**Algoritmos e Estruturas de Dados** — Do conceito fundamental à análise de complexidade usando Python.
- Fundamentos de algoritmos e pensamento computacional
- Análise de complexidade (Big O)
- Recursividade
- Exercícios progressivos
- Quando usar cada estrutura de dados

> **Público:** Quem deseja entender algoritmos, estruturas de dados e análise de complexidade.

---

#### [Node.js para Desenvolvimento Backend](nodejs.md)
**Node.js Backend** — Trilha completa de JavaScript para backend com Node.js.
- Lógica de programação e JavaScript moderno
- Assincronicidade, Promises e async/await
- Manipulação de erros
- Orientação a objetos em JavaScript
- Express.js e criação de APIs
- Autenticação e autorização
- TypeScript e NestJS
- Testes e boas práticas SOLID
- Microsserviços e WebSockets

> **Público:** Desenvolvedores que desejam aprender ou aprofundar-se em Node.js backend.

---

### 🗂️ Estruturas de Dados e Algoritmos

#### [Algoritmos e Estruturas de Dados](algoritmos_estruturas_dados.md)
**Dos conceitos fundamentais à análise de complexidade** — Entender por que uma solução funciona, quanto custa e quando usá-la.
- Fundamentos de algoritmos
- Pensamento computacional
- Recursividade
- Análise de complexidade (Big O)
- Exercícios progressivos

> **Público:** Estudantes e desenvolvedores que desejam solidificar conhecimento em algoritmos.

---

### 🌐 APIs e Comunicação

#### [API — Apostila Didática](API.md)
**Fundamentos de APIs** — Como sistemas conversam, como construir e consumir APIs.
- O que é uma API
- HTTP, requisições e respostas
- API REST: padrões e boas práticas
- Testes de endpoints
- Autenticação e autorização em APIs
- Padrões de integração (polling, webhooks)
- GraphQL, gRPC e WebSocket

> **Público:** Quem trabalha com desenvolvimento de APIs ou integração de sistemas.

---

### 🏗️ Arquitetura de Sistemas

#### [Infraestrutura para Microsserviços](microsservicos.md)
**API Gateway e Arquitetura de Microsserviços** — Como aplicações modernas são divididas, hospedadas e conectadas.
- Diferenças entre aplicações monolíticas e distribuídas
- API Gateway e seu papel
- Padrões de microsserviços (Sidecar, Strangler, CQRS)
- Banco de dados por serviço
- Service discovery
- Comunicação síncrona vs. assíncrona
- Falhas em sistemas distribuídos

> **Público:** Arquitetos, leads técnicos e desenvolvedores que trabalham com microsserviços.

---

#### [System Design Essencial](system_design_essencial_apostila_didatica.md)
**Guia prático de System Design para desenvolvedores** — Conceitos que mais importam na arquitetura de sistemas.
- Fluxo de requisições
- Escalabilidade e balanceamento de carga
- Cache em sistemas
- Bancos de dados escaláveis
- Filas de mensagens
- Hashing consistente
- Geração de IDs distribuídos
- Encurtador de URL (exemplo prático)
- Rate limiting
- Sistemas em tempo real

> **Público:** Quem estuda ou trabalha com design de sistemas e preparação para entrevistas técnicas.

---

### 🛠️ Infraestrutura e DevOps

#### [Docker para Desenvolvimento Backend](docker.md)
**Containers, imagens e boas práticas** — Do problema que Docker resolve até produção.
- O que é Docker e por que usar
- Containers, imagens e Docker Engine
- Dockerfile e construção de imagens
- Portas e aplicações web em containers
- Persistência de dados com volumes
- Redes Docker e comunicação entre containers
- Docker Compose
- Exemplo: backend com API, banco e cache
- Debug, inspeção e limpeza
- Boas práticas para desenvolvimento e produção

> **Público:** Desenvolvedores e DevOps que trabalham com containerização.

---

#### [Docker Compose](docker_compose.md)
**Orquestração de múltiplos containers** — Defina e execute aplicações multi-container com facilidade.
- Sintaxe e estrutura do `docker-compose.yml`
- Serviços, redes e volumes
- Variáveis de ambiente
- Exemplos práticos de stack local
- Diferenças entre desenvolvimento e produção

> **Público:** Quem precisa subir ambientes locais complexos ou fazer deploy de múltiplos serviços.

---

#### [CI/CD com GitHub Actions](ci_cd_github_actions.md)
**Pipelines automatizadas para projetos backend** — Validação, testes e deploy automático.
- O que é CI/CD e por que importa
- Como GitHub Actions funciona
- Preparando repositório para CI
- Primeira pipeline de CI
- Testes automatizados
- Integração com Docker e Docker Compose
- Deploy automático
- Status checks e proteção de branches

> **Público:** Desenvolvedores que desejam automatizar testes e deploy.

---

#### [Linux para Backend e Infraestrutura](linux.md)
**Comandos e conceitos essenciais para backend** — Dominar o terminal Linux para desenvolvimento profissional.
- O que é Linux e por que importa para backend
- Terminal, shell e comandos básicos
- Sistema de arquivos e permissões
- Gerenciamento de processos
- Variáveis de ambiente
- Usuários e permissões
- Redes no Linux
- Logs e diagnóstico
- Automação com shell scripts

> **Público:** Desenvolvedores que trabalham em servidores Linux ou usam containers.

---

### 📊 Dados e Mensageria

#### [SQL — Apostila Didática](sql.md)
**Fundamentos de SQL** — Pensar em dados antes de pensar em comandos.
- Conceitos básicos de bancos relacionais
- `SELECT`: consultas, filtros e ordenação
- `JOINs`: combinando dados de múltiplas tabelas
- Agregação e agrupamento
- `INSERT`, `UPDATE` e `DELETE`
- Transações e integridade
- Índices e performance
- Diferenças entre PostgreSQL, MySQL, SQLite e SQL Server

> **Público:** Quem trabalha com bancos de dados ou precisa investigar dados em produção.

---

#### [Apache Kafka para Backend](kafka.md)
**Streaming de eventos e mensageria distribuída** — Entender quando usar Kafka e como implementar.
- O que Kafka resolve
- Arquitetura: brokers, tópicos, partições
- Produtores e consumidores
- Consumer groups
- Idempotência e confiabilidade
- Ordenação de mensagens
- Exemplo prático com Docker
- Integração em aplicações backend

> **Público:** Arquitetos, desenvolvedores backend e especialistas em dados que trabalham com eventos.

---

### 🌍 Redes e Protocolos

#### [Git e GitHub](git_github.md)
**Controle de versão para desenvolvimento profissional** — Entender o fluxo, não apenas memorizar comandos.
- Fundamentos de Git
- Repositórios locais e remotos
- Branches e estratégias de branching
- Merging e rebasing
- Resolução de conflitos
- Pull Requests e code review
- Desfazendo alterações com segurança
- Colaboração em equipe

> **Público:** Todos os desenvolvedores que trabalham em projetos colaborativos.

---

#### [Redes de Computadores](redes_computadores.md)
**Modelos, protocolos e comunicação web** — Fundamentos para sistemas distribuídos e backend.
- O que é uma rede de computadores
- Camadas de rede (ISO/OSI)
- IPv4, IPv6 e roteamento
- TCP, UDP e confiabilidade
- DNS e resolução de nomes
- HTTP, HTTPS e segurança
- Firewalls e NAT
- Diagnóstico com ferramentas (ping, traceroute, netstat, tcpdump)
- Troubleshooting prático

> **Público:** Desenvolvedores backend, DevOps e arquitetos que trabalham com infraestrutura.

---

## 🚀 Caminhos de Aprendizado Sugeridos

### Para Iniciantes em Programação
1. [Python](python.md) — Aprenda os fundamentos
2. [Algoritmos e Estruturas de Dados](algoritmos_estruturas_dados.md) — Entenda como pensar computacionalmente
3. [Git e GitHub](git_github.md) — Comece a versionar seu código

### Para Quem Quer Ser Desenvolvedor Backend
1. [Python](python.md) ou [Node.js](nodejs.md) — Escolha a linguagem
2. [POO em Python](poo_python.md) ou entenda POO em JavaScript
3. [API — Apostila Didática](API.md) — Aprenda a construir APIs
4. [SQL](sql.md) — Trabalhe com bancos de dados
5. [Docker](docker.md) — Containerize suas aplicações
6. [CI/CD com GitHub Actions](ci_cd_github_actions.md) — Automatize testes e deploy
7. [Infraestrutura para Microsserviços](microsservicos.md) — Entenda arquiteturas modernas

### Para Quem Quer Estudar System Design
1. [Redes de Computadores](redes_computadores.md) — Entenda comunicação
2. [API](API.md) — Fundamentos de integração
3. [SQL](sql.md) — Bancos de dados
4. [Docker e Infraestrutura](docker.md) — Containerização
5. [System Design Essencial](system_design_essencial_apostila_didatica.md) — Conceitos avançados

### Para Quem Trabalha com Infraestrutura
1. [Linux](linux.md) — Dominar a linha de comando
2. [Docker](docker.md) — Containerização
3. [Docker Compose](docker_compose.md) — Orquestração
4. [CI/CD com GitHub Actions](ci_cd_github_actions.md) — Automação
5. [Redes de Computadores](redes_computadores.md) — Diagnóstico
6. [Kafka](kafka.md) — Mensageria distribuída

---

## 🖼️ Imagens e Diagramas

As imagens estão organizadas na pasta `images/` por tópico:

```
images/
├── algoritmos/              # Diagramas de algoritmos
├── api/                    # Fluxos e exemplos de API
├── ci-cd/                  # Pipelines e workflows
├── docker/                 # Comandos e arquitetura Docker
├── docker-compose/         # Exemplos de docker-compose
├── git-github/             # Git workflows
├── infraestrutura-microsservicos/  # Arquitetura de microsserviços
├── kafka/                  # Tópicos, partições e fluxos
├── linux/                  # Comandos e estrutura
├── poo-python/             # Diagramas de classes
├── redes/                  # Camadas, protocolos e topologias
├── sql/                    # Schemas e JOINs
└── system-design/          # Arquiteturas e escala
```

---

## 💡 Boas Práticas de Estudo

1. **Leia na ordem** — Cada apostila foi estruturada para progressão lógica
2. **Pratique ao mesmo tempo** — Código só entra na cabeça quando você digita
3. **Altere os exemplos** — Mude valores, adicione condições, quebre propositalmente
4. **Explique em voz alta** — Dê mais segurança ao aprendizado
5. **Consulte quando preciso** — Use como referência durante desenvolvimento
6. **Revise conceitos** — Alguns assuntos precisam de mais de uma leitura

---

## 📝 Sobre as Apostilas

Todas as apostilas foram:
- ✅ Estruturadas em formato Markdown para fácil navegação no GitHub
- ✅ Divididas em capítulos com índice
- ✅ Focadas em prática e entendimento, não apenas teoria
- ✅ Baseadas em experiência real de desenvolvimento
- ✅ Revisadas e reorganizadas para clareza didática

---

## 🤝 Como Contribuir

Este é um projeto de documentação viva. Se você encontrar:
- ❌ Erros ou imprecisões
- 🔍 Conceitos que poderiam ser melhor explicados
- 💡 Exemplos que faltam
- 📸 Imagens que precisam ser atualizadas

Sinta-se livre para abrir issues ou pull requests!

---

## 📄 Licença

As apostilas neste repositório foram organizadas e adaptadas para fins educacionais. Sempre cite a origem e respeite as licenças de conteúdo externo.

---

## 🎓 Bom estudo!

Escolha um tópico, abra no seu editor preferido e comece a aprender. Lembre-se: **programação se aprende fazendo**, não apenas lendo.

**Dúvidas?** Consulte as apostilas, execute os exemplos localmente e pratique até que o conceito faça sentido.

---

**Última atualização:** Junho de 2026  
**Total de apostilas:** 16 módulos didáticos
