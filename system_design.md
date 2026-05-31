# System Design para Desenvolvedores Backend

**Fundamentos, padrões arquiteturais e estudos de caso para projetar sistemas escaláveis, confiáveis e fáceis de manter**

## Sobre esta apostila

Esta apostila foi criada para servir como um material de estudo prático para desenvolvedores backend que desejam entender **System Design** de maneira aplicada. O objetivo não é decorar arquiteturas prontas, mas aprender a raciocinar sobre requisitos, gargalos, disponibilidade, escalabilidade, persistência, comunicação entre serviços e decisões técnicas do dia a dia.

O material usa como referência principal a apostila/livro em PDF **System Design Interview: An Insider's Guide**, de Alex Xu, enviada como base de estudo. As explicações foram reorganizadas em uma linguagem didática, direta e voltada para backend, complementando os conceitos com exemplos práticos, pequenos trechos de código e checklists de implementação.

> **Observação sobre imagens:** as imagens desta apostila foram extraídas do PDF de referência enviado. Antes de publicar este material em um repositório público no GitHub, revise a licença de uso da obra e confirme se a redistribuição das imagens é permitida.

## Como estudar por esta apostila

Leia os capítulos na ordem. System Design funciona como uma escada: primeiro você entende uma aplicação simples, depois adiciona balanceamento, cache, banco replicado, filas, sharding, observabilidade e tolerância a falhas. Ao final de cada capítulo, tente responder aos exercícios sem consultar a resposta imediatamente.

Sempre que aparecer um diagrama, olhe para ele como um backend developer: identifique **quem recebe a requisição**, **quem processa a regra de negócio**, **onde os dados são salvos**, **onde pode falhar**, **onde há gargalo** e **como escalar cada parte sem quebrar o sistema**.

## Índice

1. [O que é System Design](#capítulo-1--o-que-é-system-design)
2. [Escalando do zero a milhões de usuários](#capítulo-2--escalando-do-zero-a-milhões-de-usuários)
3. [Estimativas de capacidade](#capítulo-3--estimativas-de-capacidade)
4. [Framework para desenhar sistemas](#capítulo-4--framework-para-desenhar-sistemas)
5. [APIs, contratos e camada de entrada](#capítulo-5--apis-contratos-e-camada-de-entrada)
6. [Cache, CDN e performance](#capítulo-6--cache-cdn-e-performance)
7. [Bancos de dados, replicação e sharding](#capítulo-7--bancos-de-dados-replicação-e-sharding)
8. [Mensageria e comunicação assíncrona](#capítulo-8--mensageria-e-comunicação-assíncrona)
9. [Rate limiting](#capítulo-9--rate-limiting)
10. [Hashing consistente](#capítulo-10--hashing-consistente)
11. [Key-value store e sistemas distribuídos](#capítulo-11--key-value-store-e-sistemas-distribuídos)
12. [Geração de IDs e encurtador de URLs](#capítulo-12--geração-de-ids-e-encurtador-de-urls)
13. [Notificações, chat e presença online](#capítulo-13--notificações-chat-e-presença-online)
14. [Busca, autocomplete e crawling](#capítulo-14--busca-autocomplete-e-crawling)
15. [Streaming, arquivos e sincronização](#capítulo-15--streaming-arquivos-e-sincronização)
16. [Observabilidade, confiabilidade e operação](#capítulo-16--observabilidade-confiabilidade-e-operação)
17. [Aplicando System Design em projetos backend](#capítulo-17--aplicando-system-design-em-projetos-backend)
18. [Cheat sheet de System Design](#capítulo-18--cheat-sheet-de-system-design)
19. [Referências bibliográficas](#referências-bibliográficas)

---

# Capítulo 1 — O que é System Design

System Design é o processo de projetar a arquitetura de um sistema de software considerando requisitos funcionais, requisitos não funcionais, restrições técnicas, crescimento esperado, confiabilidade e operação em produção.

Para um desenvolvedor backend, System Design é a habilidade de responder perguntas como:

- Como uma API deve receber e processar requisições?
- Onde salvar os dados?
- Como evitar que o banco vire gargalo?
- Como lidar com milhões de usuários?
- Como escalar sem quebrar contratos existentes?
- Como manter o sistema funcionando mesmo quando algum componente falhar?
- Como monitorar, debugar e evoluir a aplicação em produção?

Ao final deste capítulo, você será capaz de:

- entender o papel do backend em uma arquitetura distribuída;
- diferenciar requisito funcional de requisito não funcional;
- reconhecer trade-offs comuns em decisões arquiteturais;
- começar a raciocinar sobre sistemas de forma estruturada.

## 1.1 — O problema que System Design resolve

Quando estamos aprendendo programação, normalmente pensamos em uma aplicação pequena: uma API, um banco de dados e algumas rotas. Isso funciona bem em projetos locais, estudos e MVPs. O problema começa quando o sistema precisa lidar com mais usuários, mais dados, mais regras de negócio, mais times trabalhando no mesmo código e mais exigência de disponibilidade.

Um sistema real precisa considerar fatores que não aparecem em exemplos simples:

- picos de acesso;
- falhas de rede;
- banco indisponível;
- cache desatualizado;
- mensagens duplicadas;
- deploy com rollback;
- logs e métricas;
- versionamento de APIs;
- consistência eventual;
- segurança e autorização;
- custo de infraestrutura.

System Design ajuda a transformar uma solução que “funciona na minha máquina” em uma solução que pode funcionar de forma previsível em produção.

## 1.2 — Requisitos funcionais e não funcionais

Um **requisito funcional** descreve o que o sistema precisa fazer. Por exemplo, em um sistema de pedidos:

- criar pedido;
- listar pedidos do usuário;
- cancelar pedido;
- enviar notificação de pagamento;
- consultar status da entrega.

Um **requisito não funcional** descreve como o sistema precisa se comportar. Exemplos:

- responder em até 200 ms para 95% das requisições;
- suportar 5.000 requisições por segundo;
- ficar disponível 99,9% do tempo;
- não perder pedidos em caso de falha parcial;
- permitir deploy sem downtime.

Para backend, os requisitos não funcionais são decisivos. A mesma rota `POST /orders` pode ser simples em um e-commerce pequeno e extremamente complexa em uma plataforma com milhões de usuários, antifraude, pagamento, estoque, cupom, nota fiscal e logística.

## 1.3 — Trade-offs: quase toda decisão tem custo

Em System Design, raramente existe uma resposta perfeita. Normalmente existe uma decisão adequada para determinado contexto.

Por exemplo:

| Decisão | Benefício | Custo |
|---|---|---|
| Usar cache | Reduz latência e carga no banco | Pode gerar dados desatualizados |
| Usar fila | Desacopla serviços e melhora resiliência | Aumenta complexidade operacional |
| Usar microservices | Permite deploy e escala independentes | Exige observabilidade, contratos e rede |
| Usar sharding | Distribui dados em vários bancos | Dificulta joins e consultas globais |
| Usar consistência forte | Evita leitura de dado antigo | Pode aumentar latência e reduzir disponibilidade |

O ponto central é: **não escolha tecnologia antes de entender o problema**.

## 1.4 — Exemplo simples: uma API de pedidos

Imagine uma API backend que cria pedidos:

```http
POST /orders
Content-Type: application/json
Authorization: Bearer <token>

{
  "customer_id": "123",
  "items": [
    {"product_id": "A10", "quantity": 2},
    {"product_id": "B20", "quantity": 1}
  ]
}
```

Em um projeto pequeno, talvez você grave tudo em uma tabela e retorne `201 Created`. Em um sistema real, o backend pode precisar:

1. validar autenticação;
2. validar estoque;
3. calcular preço;
4. aplicar cupom;
5. criar pedido;
6. iniciar pagamento;
7. reservar estoque;
8. publicar evento `OrderCreated`;
9. enviar notificação;
10. registrar logs e métricas.

Isso mostra que System Design não é apenas “desenhar caixinhas”. É entender o fluxo completo de dados e responsabilidades.

## 1.5 — Perguntas que um backend deve fazer

Antes de desenhar qualquer arquitetura, pergunte:

- Qual é o fluxo principal do usuário?
- Qual é o volume esperado de leitura e escrita?
- Quais dados são críticos e não podem ser perdidos?
- O sistema precisa responder imediatamente ou pode processar em segundo plano?
- O que acontece se o banco cair?
- O que acontece se uma mensagem for processada duas vezes?
- Existe requisito de auditoria?
- O sistema precisa ser multi-região?
- Qual é o limite aceitável de latência?

Essas perguntas reduzem ambiguidades e evitam overengineering.

## 1.6 — Resumo do capítulo

System Design é a prática de projetar sistemas pensando em requisitos, escala, falhas, dados, comunicação, operação e evolução. Para backend, essa habilidade é fundamental porque boa parte dos problemas de produção ocorre justamente nas fronteiras entre API, banco, cache, fila, rede e infraestrutura.

## 1.7 — Exercícios

1. Escolha uma API que você já desenvolveu e liste seus requisitos funcionais.
2. Liste pelo menos cinco requisitos não funcionais para essa API.
3. Descreva o que aconteceria se o banco de dados ficasse indisponível por 5 minutos.
4. Identifique uma decisão técnica que melhoraria performance, mas aumentaria complexidade.

---

# Capítulo 2 — Escalando do zero a milhões de usuários

Este capítulo mostra a evolução natural de uma aplicação backend: começamos com um único servidor e adicionamos componentes conforme surgem gargalos. Esse é um dos caminhos mais importantes para entender System Design, porque mostra que arquitetura nasce de necessidade, não de moda.

Ao final deste capítulo, você será capaz de:

- entender por que uma aplicação simples deixa de escalar;
- explicar o papel de DNS, load balancer, cache, CDN, banco replicado e filas;
- diferenciar escala vertical e horizontal;
- reconhecer pontos únicos de falha;
- visualizar a evolução de uma arquitetura backend.

## 2.1 — Começando com um único servidor

Em uma aplicação muito pequena, tudo pode rodar no mesmo servidor: aplicação web, API, banco de dados, cache e arquivos estáticos. Essa abordagem é simples, barata e fácil de entender.

![Figura 2.1 - Aplicação inicial em servidor único extraída do PDF de referência.](./images/system-design/01_single_server_setup.png)

*Figura 2.1 - Aplicação inicial em servidor único extraída do PDF de referência.*

O fluxo de uma requisição simples normalmente é:

1. o usuário acessa um domínio, como `api.exemplo.com`;
2. o DNS resolve esse domínio para um endereço IP;
3. o navegador ou aplicativo mobile envia uma requisição HTTP;
4. o servidor processa a requisição;
5. o servidor retorna HTML, JSON ou outro formato de resposta.

![Figura 2.2 - Fluxo DNS e requisição HTTP extraído do PDF de referência.](./images/system-design/02_dns_request_flow.png)

*Figura 2.2 - Fluxo DNS e requisição HTTP extraído do PDF de referência.*

Essa arquitetura é adequada para estudos e sistemas internos pequenos. Porém, ela tem limitações claras. Se o servidor cair, tudo cai. Se o banco consumir muita CPU, a API fica lenta. Se o tráfego aumentar, todos os componentes disputam os mesmos recursos.

## 2.2 — Separando aplicação e banco de dados

O primeiro passo comum é separar a aplicação do banco de dados. A camada web/API fica em um servidor, e a camada de dados fica em outro.

![Figura 2.3 - Separação entre camada web e camada de dados extraída do PDF de referência.](./images/system-design/03_web_tier_data_tier.png)

*Figura 2.3 - Separação entre camada web e camada de dados extraída do PDF de referência.*

Essa separação permite escalar e administrar cada parte de forma independente. Se a API precisa de mais CPU, escalamos a camada de aplicação. Se o banco precisa de mais memória ou disco, ajustamos a camada de dados.

Para backend, essa separação também ajuda a organizar responsabilidades:

- **API/backend:** valida entrada, executa regra de negócio, autentica usuário, chama serviços externos e monta resposta.
- **Banco de dados:** persiste estado, garante integridade e responde consultas.

## 2.3 — SQL ou NoSQL?

Bancos relacionais, como PostgreSQL, MySQL e SQL Server, armazenam dados em tabelas, possuem SQL, suportam transações e são excelentes quando o domínio tem relacionamentos claros.

Bancos NoSQL podem ser úteis quando:

- os dados são muito volumosos;
- o modelo é mais flexível ou semi-estruturado;
- a aplicação precisa de baixa latência em escala alta;
- o acesso é simples, geralmente por chave;
- o sistema prioriza disponibilidade e distribuição.

Uma boa regra prática: **comece com banco relacional quando o domínio ainda está evoluindo e as relações importam**. Use NoSQL quando o padrão de acesso, volume ou latência justificar.

## 2.4 — Escala vertical e horizontal

Escalar verticalmente significa aumentar os recursos da mesma máquina: mais CPU, RAM, disco ou I/O. É simples, mas tem limite físico e pode manter o sistema dependente de um único servidor.

Escalar horizontalmente significa adicionar mais servidores. Em vez de uma máquina muito grande, usamos várias instâncias menores distribuindo a carga.

![Figura 2.4 - Comparação entre escala vertical e horizontal extraída do PDF de referência.](./images/system-design/20_vertical_vs_horizontal_scaling.png)

*Figura 2.4 - Comparação entre escala vertical e horizontal extraída do PDF de referência.*

Para sistemas grandes, a escala horizontal é mais comum porque aumenta disponibilidade e permite adicionar capacidade conforme a demanda cresce.

## 2.5 — Load balancer

Quando existem várias instâncias da API, precisamos de um componente para distribuir tráfego. Esse componente é o **load balancer**.

![Figura 2.5 - Load balancer distribuindo requisições entre servidores extraído do PDF de referência.](./images/system-design/04_load_balancer.png)

*Figura 2.5 - Load balancer distribuindo requisições entre servidores extraído do PDF de referência.*

O load balancer recebe requisições dos clientes e encaminha para instâncias saudáveis da aplicação. Ele ajuda em três pontos:

- **distribuição de carga:** evita que uma única instância receba todo o tráfego;
- **alta disponibilidade:** se uma instância cair, o tráfego vai para outra;
- **segurança:** as instâncias internas podem ficar em rede privada.

Como backend, você precisa tomar cuidado para que suas instâncias sejam **stateless**. Se uma instância guarda sessão localmente, o usuário pode falhar ao cair em outra instância.

## 2.6 — Banco replicado

Depois de escalar a camada web, o próximo gargalo costuma ser o banco de dados. Uma técnica comum é replicação.

![Figura 2.6 - Replicação de banco de dados master/slave extraída do PDF de referência.](./images/system-design/05_database_replication.png)

*Figura 2.6 - Replicação de banco de dados master/slave extraída do PDF de referência.*

No modelo clássico, existe um nó principal para escrita e réplicas para leitura. Escritas, atualizações e exclusões vão para o banco principal. Leituras podem ser distribuídas entre réplicas.

Benefícios:

- melhora performance de leitura;
- aumenta disponibilidade;
- reduz risco de perda total de dados;
- permite failover em caso de falha.

Cuidados:

- réplicas podem ficar atrasadas;
- leituras podem retornar dados antigos;
- failover exige automação e testes;
- nem toda query deve ir para réplica.

Exemplo prático:

```python
# ideia conceitual: escolha de conexão por tipo de operação

def get_db_connection(operation: str):
    if operation in {"insert", "update", "delete"}:
        return primary_database
    return read_replica_pool.get_connection()
```

Esse código é apenas ilustrativo. Na prática, ORMs, proxies ou bibliotecas de conexão podem fazer parte desse roteamento.

## 2.7 — Arquitetura com load balancer e banco replicado

![Figura 2.7 - Load balancer, camada web e banco replicado extraídos do PDF de referência.](./images/system-design/06_lb_db_replication.png)

*Figura 2.7 - Load balancer, camada web e banco replicado extraídos do PDF de referência.*

Neste ponto, o sistema já tem uma separação importante:

- usuários acessam o load balancer;
- o load balancer distribui para servidores web/API;
- a API envia escritas para o banco principal;
- a API envia leituras para réplicas.

A arquitetura ainda pode sofrer com latência e carga no banco. O próximo passo é cache.

## 2.8 — Cache

Cache é uma camada temporária, normalmente em memória, usada para armazenar dados acessados com frequência ou respostas caras de calcular.

![Figura 2.8 - Camada de cache entre aplicação e banco extraída do PDF de referência.](./images/system-design/07_cache_tier.png)

*Figura 2.8 - Camada de cache entre aplicação e banco extraída do PDF de referência.*

Fluxo comum de cache-aside:

1. a API recebe a requisição;
2. tenta buscar a resposta no cache;
3. se encontrou, retorna rapidamente;
4. se não encontrou, busca no banco;
5. salva no cache com TTL;
6. retorna ao cliente.

Exemplo simplificado:

```python
def get_product(product_id: str):
    cache_key = f"product:{product_id}"

    cached = redis.get(cache_key)
    if cached:
        return json.loads(cached)

    product = database.find_product(product_id)
    redis.setex(cache_key, 300, json.dumps(product))
    return product
```

Use cache quando o dado é lido com frequência e muda pouco. Evite cache para dados que precisam estar sempre atualizados, a menos que você tenha uma estratégia clara de invalidação.

## 2.9 — Ponto único de falha

Um ponto único de falha é qualquer componente que, ao falhar, interrompe o sistema inteiro.

![Figura 2.9 - Exemplo de ponto único de falha extraído do PDF de referência.](./images/system-design/08_single_point_failure.png)

*Figura 2.9 - Exemplo de ponto único de falha extraído do PDF de referência.*

Exemplos de pontos únicos de falha:

- um único banco sem réplica;
- um único load balancer;
- um único servidor de cache obrigatório;
- um único broker de mensagens;
- um único data center;
- um único serviço crítico sem fallback.

Em produção, componentes críticos precisam de redundância, health checks e estratégia de failover.

## 2.10 — CDN

CDN é uma rede de servidores distribuídos geograficamente para entregar conteúdo próximo do usuário. É muito usada para arquivos estáticos: imagens, CSS, JavaScript, vídeos, downloads e assets públicos.

![Figura 2.10 - CDN reduzindo latência geográfica extraída do PDF de referência.](./images/system-design/09_cdn_latency.png)

*Figura 2.10 - CDN reduzindo latência geográfica extraída do PDF de referência.*

![Figura 2.11 - Fluxo de busca e cache em CDN extraído do PDF de referência.](./images/system-design/10_cdn_workflow.png)

*Figura 2.11 - Fluxo de busca e cache em CDN extraído do PDF de referência.*

Para backend, CDN reduz tráfego na origem e melhora experiência do usuário. Em APIs, a CDN também pode ser usada para alguns conteúdos cacheáveis, mas é preciso cuidado com autenticação, headers, cookies e dados personalizados.

## 2.11 — Arquitetura com CDN e cache

![Figura 2.12 - Arquitetura com CDN e cache extraída do PDF de referência.](./images/system-design/11_cdn_cache_architecture.png)

*Figura 2.12 - Arquitetura com CDN e cache extraída do PDF de referência.*

Agora os arquivos estáticos saem da CDN e os dados frequentes saem do cache. Isso reduz carga nos servidores web e no banco de dados.

## 2.12 — Stateful vs stateless

Um servidor **stateful** guarda estado local entre requisições. Por exemplo, sessão do usuário salva na memória da instância.

![Figura 2.13 - Arquitetura stateful extraída do PDF de referência.](./images/system-design/12_stateful_web_tier.png)

*Figura 2.13 - Arquitetura stateful extraída do PDF de referência.*

O problema é que o usuário precisa cair sempre na mesma instância. Isso pode exigir sticky sessions e dificulta escalabilidade.

Um servidor **stateless** não depende de estado local. Qualquer instância pode atender qualquer requisição. O estado fica em um armazenamento compartilhado: banco, Redis, NoSQL ou token assinado.

![Figura 2.14 - Arquitetura stateless extraída do PDF de referência.](./images/system-design/13_stateless_web_tier.png)

*Figura 2.14 - Arquitetura stateless extraída do PDF de referência.*

Para backend moderno, prefira APIs stateless sempre que possível. Isso facilita autoscaling, deploys, balanceamento e recuperação de falhas.

![Figura 2.15 - Camada web stateless com autoscaling extraída do PDF de referência.](./images/system-design/14_stateless_autoscale_architecture.png)

*Figura 2.15 - Camada web stateless com autoscaling extraída do PDF de referência.*

## 2.13 — Múltiplos data centers

Quando o sistema cresce globalmente, pode ser necessário operar em múltiplos data centers ou regiões de nuvem.

![Figura 2.16 - Arquitetura com múltiplos data centers extraída do PDF de referência.](./images/system-design/15_multi_datacenter.png)

*Figura 2.16 - Arquitetura com múltiplos data centers extraída do PDF de referência.*

![Figura 2.17 - Failover entre data centers extraído do PDF de referência.](./images/system-design/16_datacenter_failover.png)

*Figura 2.17 - Failover entre data centers extraído do PDF de referência.*

Essa arquitetura melhora latência e disponibilidade, mas aumenta complexidade:

- replicação entre regiões;
- roteamento geográfico;
- consistência de dados;
- custo de infraestrutura;
- deploy coordenado;
- testes de desastre.

Em muitos projetos backend, não faz sentido começar multi-região. Mas é importante entender o conceito para sistemas críticos.

## 2.14 — Fila de mensagens

Filas desacoplam serviços e permitem processamento assíncrono.

![Figura 2.18 - Modelo produtor, fila e consumidor extraído do PDF de referência.](./images/system-design/17_message_queue_basic.png)

*Figura 2.18 - Modelo produtor, fila e consumidor extraído do PDF de referência.*

![Figura 2.19 - Workers consumindo jobs assíncronos extraídos do PDF de referência.](./images/system-design/18_message_queue_workers.png)

*Figura 2.19 - Workers consumindo jobs assíncronos extraídos do PDF de referência.*

Um exemplo comum é processamento de imagem. A API recebe o upload, salva metadados e publica um job. Workers consomem a fila e fazem o processamento pesado em segundo plano.

Vantagens:

- reduz latência da requisição principal;
- absorve picos de carga;
- permite escalar workers independentemente;
- aumenta resiliência quando consumidores ficam temporariamente indisponíveis.

## 2.15 — Logs, métricas, monitoramento e automação

![Figura 2.20 - Arquitetura com fila, workers e ferramentas de operação extraída do PDF de referência.](./images/system-design/19_mq_logging_metrics_automation.png)

*Figura 2.20 - Arquitetura com fila, workers e ferramentas de operação extraída do PDF de referência.*

Conforme o sistema cresce, não basta “subir servidores”. É preciso operar o sistema. Isso inclui:

- logs centralizados;
- métricas técnicas e de negócio;
- alertas;
- health checks;
- deploy automatizado;
- rollback;
- CI/CD;
- dashboards.

Um sistema que não é observável é difícil de manter, mesmo que sua arquitetura pareça bonita.

## 2.16 — Sharding

Quando o banco cresce demais para uma única máquina, podemos particionar os dados em vários shards.

![Figura 2.21 - Distribuição de usuários em shards usando função de hash extraída do PDF de referência.](./images/system-design/21_sharding_hash_function.png)

*Figura 2.21 - Distribuição de usuários em shards usando função de hash extraída do PDF de referência.*

![Figura 2.22 - Tabelas distribuídas em shards extraídas do PDF de referência.](./images/system-design/22_sharded_database_tables.png)

*Figura 2.22 - Tabelas distribuídas em shards extraídas do PDF de referência.*

No exemplo, `user_id % 4` decide em qual shard o usuário ficará. Em sistemas reais, a escolha da chave de particionamento é crítica.

Uma boa sharding key deve:

- distribuir dados de forma uniforme;
- ser conhecida no momento da consulta;
- evitar hot partitions;
- reduzir consultas cross-shard;
- acompanhar o principal padrão de acesso.

Sharding resolve escala de dados, mas traz dificuldades: joins entre shards, rebalancing, transações distribuídas e roteamento de consultas.

![Figura 2.23 - Arquitetura com banco sharded extraída do PDF de referência.](./images/system-design/23_sharded_architecture.png)

*Figura 2.23 - Arquitetura com banco sharded extraída do PDF de referência.*

## 2.17 — Resumo do capítulo

A evolução típica de uma arquitetura backend segue este caminho:

1. servidor único;
2. separação entre aplicação e banco;
3. load balancer;
4. múltiplas instâncias da API;
5. banco replicado;
6. cache;
7. CDN;
8. camada web stateless;
9. filas e workers;
10. observabilidade;
11. sharding;
12. múltiplas regiões quando necessário.

## 2.18 — Exercícios

1. Desenhe a arquitetura atual de uma API simples que você já criou.
2. Identifique o primeiro gargalo provável se ela recebesse 10 vezes mais tráfego.
3. Explique quando você usaria cache nessa API.
4. Liste quais componentes seriam pontos únicos de falha.
5. Proponha uma versão stateless dessa API.

---

# Capítulo 3 — Estimativas de capacidade

Estimativas de capacidade ajudam a validar se uma arquitetura faz sentido antes de implementar. Em System Design, você não precisa calcular com precisão absoluta, mas precisa entender ordem de grandeza.

Ao final deste capítulo, você será capaz de:

- estimar QPS;
- calcular armazenamento aproximado;
- estimar tráfego de rede;
- entender disponibilidade;
- usar aproximações durante decisões de arquitetura.

## 3.1 — Unidades de dados

![Figura 3.1 - Tabela de potências de dois extraída do PDF de referência.](./images/system-design/24_power_of_two_table.png)

*Figura 3.1 - Tabela de potências de dois extraída do PDF de referência.*

Regras práticas:

- 1 KB ≈ mil bytes;
- 1 MB ≈ um milhão de bytes;
- 1 GB ≈ um bilhão de bytes;
- 1 TB ≈ um trilhão de bytes;
- 1 PB ≈ mil TB.

Em entrevistas e planejamentos iniciais, arredondamentos são aceitáveis. O objetivo é entender se você precisa de megabytes, gigabytes, terabytes ou petabytes.

## 3.2 — Números de latência

![Figura 3.2 - Tabela de latências típicas extraída do PDF de referência.](./images/system-design/25_latency_numbers_table.png)

*Figura 3.2 - Tabela de latências típicas extraída do PDF de referência.*

A principal lição é simples:

- memória é muito rápida;
- rede é mais lenta;
- disco é mais lento ainda;
- chamadas entre regiões são muito mais caras;
- compressão simples pode valer a pena antes de tráfego externo.

Para backend, isso muda decisões práticas. Uma API que faz cinco chamadas externas sequenciais pode ser muito mais lenta do que uma API que faz uma consulta bem indexada no banco.

## 3.3 — Disponibilidade

![Figura 3.3 - Tabela de disponibilidade e downtime extraída do PDF de referência.](./images/system-design/26_availability_numbers_table.png)

*Figura 3.3 - Tabela de disponibilidade e downtime extraída do PDF de referência.*

Disponibilidade mede quanto tempo o sistema fica operacional. Exemplos aproximados:

| Disponibilidade | Downtime aproximado por ano |
|---|---:|
| 99% | 3,65 dias |
| 99,9% | 8,77 horas |
| 99,99% | 52,6 minutos |
| 99,999% | 5,26 minutos |

Quanto mais “noves”, maior o custo e a complexidade. Alta disponibilidade exige redundância, monitoramento, failover, testes e operação madura.

## 3.4 — Estimando QPS

QPS significa **queries per second**, ou requisições por segundo. Uma fórmula simples:

```text
QPS = número de operações por dia / 86.400 segundos
```

Exemplo:

```text
Usuários ativos por dia: 1.000.000
Cada usuário faz 20 requisições por dia
Total diário: 20.000.000 requisições
QPS médio = 20.000.000 / 86.400 ≈ 231 req/s
Pico estimado = 2x ou 3x o QPS médio ≈ 462 a 693 req/s
```

O pico é mais importante que a média, porque infraestrutura normalmente falha em horários concentrados.

## 3.5 — Estimando armazenamento

Exemplo: sistema de logs de requisições.

```text
Requisições por dia: 50.000.000
Tamanho médio de cada log: 1 KB
Armazenamento diário: 50.000.000 KB ≈ 50 GB/dia
Retenção: 30 dias
Total: 50 GB * 30 = 1,5 TB
```

Depois dessa estimativa, você decide se vai usar banco relacional, armazenamento de objetos, solução de logs, compressão ou política de retenção.

## 3.6 — Estimando cache

Se uma API de produtos recebe 1.000 req/s e 80% das consultas são para 20% dos produtos, cache pode reduzir drasticamente a carga no banco.

```text
QPS total: 1.000
Cache hit ratio: 80%
Requisições atendidas pelo cache: 800 req/s
Requisições que chegam no banco: 200 req/s
```

A métrica importante é o **cache hit ratio**: percentual de requisições respondidas pelo cache.

## 3.7 — Estimando servidores

Se uma instância da API suporta 250 req/s com segurança, e o pico esperado é 1.000 req/s:

```text
Instâncias necessárias = 1.000 / 250 = 4
Com margem de segurança de 50%: 6 instâncias
```

Na prática, você também considera CPU, memória, conexões, latência, p95/p99, autoscaling e custo.

## 3.8 — Resumo do capítulo

Estimativas ajudam a justificar decisões. Antes de escolher banco, cache, fila ou sharding, estime volume, QPS, armazenamento, latência e disponibilidade. O objetivo é reduzir decisões no escuro.

## 3.9 — Exercícios

1. Estime o QPS médio de um app com 500 mil usuários ativos por dia e 12 ações por usuário.
2. Estime o armazenamento mensal de imagens se 10 mil usuários enviam 3 imagens de 2 MB por dia.
3. Calcule o downtime anual aproximado de um sistema com 99,9% de disponibilidade.
4. Uma API suporta 100 req/s por instância. Quantas instâncias são necessárias para pico de 1.200 req/s com 30% de folga?

---

# Capítulo 4 — Framework para desenhar sistemas

System Design pode parecer amplo demais. Para organizar o raciocínio, use um framework simples.

Ao final deste capítulo, você será capaz de:

- conduzir uma discussão de arquitetura;
- levantar requisitos antes da solução;
- fazer estimativas;
- desenhar componentes principais;
- aprofundar gargalos e trade-offs.

## 4.1 — Passo 1: entender o problema

Nunca comece escolhendo tecnologia. Comece fazendo perguntas:

- Quais funcionalidades são obrigatórias?
- Quem são os usuários?
- Qual é o volume esperado?
- Quais operações são mais comuns: leitura ou escrita?
- Qual latência é aceitável?
- O sistema precisa funcionar offline?
- Existe dado sensível?
- A consistência precisa ser forte ou eventual?
- Qual é o SLA esperado?

Exemplo: “desenhar um sistema de chat” é vago. Você precisa saber se será chat 1:1, grupos, envio de mídia, presença online, histórico, push notification, criptografia, busca e escala global.

## 4.2 — Passo 2: definir requisitos funcionais

Exemplo para um encurtador de URLs:

- criar URL curta;
- redirecionar URL curta para URL original;
- expirar links;
- consultar estatísticas;
- bloquear URLs maliciosas.

## 4.3 — Passo 3: definir requisitos não funcionais

Exemplo:

- baixa latência no redirecionamento;
- alta disponibilidade;
- leitura muito maior que escrita;
- links não podem colidir;
- sistema deve suportar milhões de URLs;
- dados devem ser persistidos.

## 4.4 — Passo 4: estimar escala

Antes de desenhar, estime:

- QPS de escrita;
- QPS de leitura;
- armazenamento;
- tráfego de rede;
- tamanho de cache;
- crescimento anual.

## 4.5 — Passo 5: definir APIs

APIs ajudam a transformar requisitos em contratos.

```http
POST /short-urls
Content-Type: application/json

{
  "original_url": "https://exemplo.com/artigo/system-design",
  "expires_at": "2026-12-31T23:59:59Z"
}
```

```http
GET /{short_code}
```

```http
GET /short-urls/{short_code}/stats
```

## 4.6 — Passo 6: modelar dados

Para o exemplo de encurtador:

```sql
CREATE TABLE short_urls (
    id BIGSERIAL PRIMARY KEY,
    short_code VARCHAR(20) UNIQUE NOT NULL,
    original_url TEXT NOT NULL,
    user_id BIGINT,
    created_at TIMESTAMP NOT NULL DEFAULT now(),
    expires_at TIMESTAMP
);
```

A modelagem precisa seguir os principais padrões de acesso. Se a operação mais importante é redirecionar, `short_code` precisa ser indexado.

## 4.7 — Passo 7: desenho de alto nível

Um desenho inicial geralmente contém:

- cliente;
- DNS;
- load balancer;
- API;
- banco;
- cache;
- fila;
- workers;
- armazenamento de objetos;
- serviços externos.

## 4.8 — Passo 8: aprofundar gargalos

Depois do desenho alto nível, aprofunde os pontos críticos:

- O banco aguenta o volume?
- O cache pode ficar inconsistente?
- O que acontece se a fila crescer demais?
- Como evitar processamento duplicado?
- Como fazer deploy sem downtime?
- Onde ficam logs e métricas?
- Como proteger endpoints críticos?

## 4.9 — Passo 9: revisar trade-offs

Finalize explicando por que escolheu determinada arquitetura e o que ficou fora do escopo.

Exemplo:

> “Começaria com PostgreSQL e Redis porque o volume inicial não justifica sharding. Se a leitura crescer muito, adicionaria réplicas de leitura e cache. Se a escrita de eventos crescer, moveria estatísticas para processamento assíncrono.”

## 4.10 — Resumo do capítulo

Um bom design não começa pela ferramenta. Começa por requisitos, estimativas e trade-offs. O framework é: **entender problema → definir requisitos → estimar escala → desenhar APIs → modelar dados → desenhar arquitetura → aprofundar gargalos → revisar trade-offs**.

## 4.11 — Exercícios

1. Aplique esse framework para “sistema de comentários”.
2. Defina APIs para “sistema de favoritos”.
3. Modele as tabelas principais de um “sistema de notificações”.
4. Liste gargalos prováveis de uma API de login.

---

# Capítulo 5 — APIs, contratos e camada de entrada

A camada de entrada é a porta pela qual usuários, frontends, aplicativos mobile e sistemas externos acessam seu backend. Em arquiteturas simples, o cliente chama a API diretamente. Em arquiteturas maiores, pode existir load balancer, API Gateway, BFF, autenticação centralizada, WAF, rate limiting e observabilidade.

Ao final deste capítulo, você será capaz de:

- entender a função da camada de entrada;
- desenhar contratos de API mais estáveis;
- reconhecer quando usar API Gateway ou BFF;
- aplicar boas práticas de versionamento e idempotência.

## 5.1 — API como contrato

Quando uma API é publicada, ela vira um contrato. Outros clientes passam a depender dela. Se você remove um campo, troca o tipo de um atributo ou altera o significado de um status code, pode quebrar integrações.

Exemplo de contrato simples:

```http
POST /orders
```

```json
{
  "customer_id": "123",
  "items": [
    {"product_id": "A10", "quantity": 2}
  ],
  "payment_method_id": "card_001"
}
```

Resposta:

```json
{
  "order_id": "ord_123",
  "status": "pending_payment",
  "created_at": "2026-05-31T12:00:00Z"
}
```

## 5.2 — Versionamento

Mudanças pequenas podem ser aditivas: adicionar campo opcional, criar novo endpoint, expandir valores possíveis. Mudanças incompatíveis precisam de versionamento.

Exemplo:

```http
GET /v1/orders/{id}
GET /v2/orders/{id}
```

Não versione tudo cedo demais, mas também não quebre clientes existentes sem plano de migração.

## 5.3 — Idempotência

Idempotência significa que repetir a mesma operação produz o mesmo efeito final. Isso é essencial em pagamentos, pedidos e integrações instáveis.

Exemplo:

```http
POST /payments
Idempotency-Key: 2d3b7e8e-97cd-4d3a-a4b5-123456789abc
```

Se o cliente repetir a chamada por timeout, o backend deve identificar a chave e não cobrar duas vezes.

Modelo simplificado:

```sql
CREATE TABLE idempotency_keys (
    key TEXT PRIMARY KEY,
    request_hash TEXT NOT NULL,
    response_body JSONB,
    status_code INT,
    created_at TIMESTAMP NOT NULL DEFAULT now()
);
```

## 5.4 — API Gateway

Um API Gateway pode centralizar responsabilidades transversais:

- autenticação;
- autorização;
- roteamento;
- rate limiting;
- logs;
- métricas;
- transformação de payload;
- agregação de respostas;
- roteamento por versão.

Use API Gateway quando há muitos serviços internos e clientes externos não devem conhecer a topologia real da arquitetura.

## 5.5 — BFF: Backend for Frontend

BFF é uma camada backend específica para um tipo de cliente. Um app mobile pode precisar de payloads menores; uma aplicação web pode precisar de dados agregados; um painel admin pode precisar de endpoints diferentes.

Em vez de forçar todos os clientes a consumir os mesmos endpoints, um BFF adapta a experiência sem poluir os serviços de domínio.

## 5.6 — Status codes úteis

| Código | Uso comum |
|---|---|
| 200 OK | Requisição bem-sucedida |
| 201 Created | Recurso criado |
| 202 Accepted | Processamento aceito e assíncrono |
| 204 No Content | Sucesso sem corpo de resposta |
| 400 Bad Request | Entrada inválida |
| 401 Unauthorized | Não autenticado |
| 403 Forbidden | Autenticado, mas sem permissão |
| 404 Not Found | Recurso não encontrado |
| 409 Conflict | Conflito de estado ou idempotência |
| 422 Unprocessable Entity | Erro semântico de validação |
| 429 Too Many Requests | Rate limit excedido |
| 500 Internal Server Error | Erro inesperado |
| 503 Service Unavailable | Serviço indisponível temporariamente |

## 5.7 — Resumo do capítulo

APIs são contratos. Um bom backend precisa desenhar entradas estáveis, versionáveis, observáveis e seguras. API Gateway e BFF ajudam quando há múltiplos serviços ou múltiplos tipos de cliente.

## 5.8 — Exercícios

1. Desenhe os endpoints principais de uma API de tarefas.
2. Liste quais campos da resposta poderiam ser adicionados sem quebrar clientes.
3. Crie um exemplo de chave de idempotência para criação de pedido.
4. Explique quando um endpoint deveria retornar `202 Accepted`.

---

# Capítulo 6 — Cache, CDN e performance

Performance não é apenas “código rápido”. Em sistemas backend, performance envolve reduzir chamadas caras, evitar trabalho repetido, aproximar dados do usuário e controlar gargalos.

Ao final deste capítulo, você será capaz de:

- escolher estratégias de cache;
- entender TTL e invalidação;
- identificar riscos de dados desatualizados;
- diferenciar cache de aplicação, cache distribuído e CDN.

## 6.1 — Quando usar cache

Use cache quando:

- o dado é lido com frequência;
- o dado muda pouco;
- a consulta é cara;
- a mesma resposta atende muitos usuários;
- latência baixa é importante;
- o banco está recebendo carga repetitiva.

Evite cache quando:

- o dado muda a cada requisição;
- consistência forte é obrigatória;
- a invalidação é mais complexa que a consulta;
- o dado é sensível e pode vazar entre usuários.

## 6.2 — Estratégia cache-aside

É uma das estratégias mais comuns em backend.

```python
def get_user_profile(user_id: str):
    key = f"user-profile:{user_id}"

    cached = redis.get(key)
    if cached is not None:
        return json.loads(cached)

    profile = db.query_user_profile(user_id)
    redis.setex(key, 600, json.dumps(profile))
    return profile
```

A aplicação controla leitura e escrita no cache. É simples, mas exige cuidado com invalidação.

## 6.3 — TTL

TTL significa tempo de vida do cache. Um TTL curto reduz risco de dado velho, mas aumenta carga no banco. Um TTL longo reduz carga, mas aumenta chance de inconsistência.

Exemplos:

| Dado | TTL sugerido |
|---|---:|
| Configuração quase estática | 1 hora a 24 horas |
| Catálogo de produtos | 1 a 10 minutos |
| Perfil público | 5 a 30 minutos |
| Saldo financeiro | Evitar cache ou TTL muito curto |
| Resultado de relatório | minutos a horas |

## 6.4 — Invalidação

Invalidação é uma das partes mais difíceis do cache. Estratégias comuns:

- remover cache quando o dado muda;
- usar TTL;
- versionar a chave;
- publicar evento de invalidação;
- rebuild assíncrono.

Exemplo:

```python
def update_product(product_id: str, payload: dict):
    db.update_product(product_id, payload)
    redis.delete(f"product:{product_id}")
```

## 6.5 — Cache stampede

Cache stampede acontece quando uma chave muito acessada expira e várias requisições tentam recalcular o mesmo dado ao mesmo tempo.

Mitigações:

- lock distribuído;
- TTL com jitter;
- refresh assíncrono;
- stale-while-revalidate;
- pré-aquecimento de cache.

Exemplo de TTL com jitter:

```python
import random

ttl = 300 + random.randint(0, 60)
redis.setex(cache_key, ttl, value)
```

## 6.6 — CDN para assets estáticos

CDN deve ser usada para arquivos estáticos e grandes, como imagens, vídeos, CSS e JavaScript. Em sistemas com upload de arquivos, uma prática comum é:

1. usuário faz upload;
2. backend valida e gera URL assinada;
3. arquivo é salvo em object storage;
4. CDN entrega o arquivo aos usuários;
5. metadados ficam no banco.

## 6.7 — Resumo do capítulo

Cache é uma ferramenta poderosa, mas não gratuita. Ele melhora performance e reduz carga, mas adiciona risco de inconsistência. Use cache quando o padrão de acesso justificar e tenha estratégia de TTL, invalidação e observabilidade.

## 6.8 — Exercícios

1. Escolha três endpoints de uma API e diga quais poderiam usar cache.
2. Defina TTL para cada endpoint escolhido.
3. Explique como invalidaria o cache ao atualizar um produto.
4. Descreva um cenário de cache stampede e como mitigá-lo.

---

# Capítulo 7 — Bancos de dados, replicação e sharding

A camada de dados costuma ser o ponto mais crítico de um sistema backend. Código pode ser reimplantado, instâncias podem ser recriadas, mas dados perdidos ou corrompidos são difíceis de recuperar.

Ao final deste capítulo, você será capaz de:

- escolher entre SQL e NoSQL com mais clareza;
- entender replicação;
- entender sharding;
- reconhecer problemas de consistência;
- projetar consultas com base nos padrões de acesso.

## 7.1 — Modele pelo padrão de acesso

Antes de criar tabelas ou coleções, responda:

- Quais consultas serão mais frequentes?
- A aplicação lê mais ou escreve mais?
- O dado precisa de transação?
- Haverá busca por texto?
- Haverá consultas por intervalo de tempo?
- Haverá joins?
- O dado precisa ser particionado por cliente, região ou usuário?

Exemplo de pedido:

```sql
CREATE TABLE orders (
    id UUID PRIMARY KEY,
    customer_id UUID NOT NULL,
    status TEXT NOT NULL,
    total_amount NUMERIC(12, 2) NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT now()
);

CREATE INDEX idx_orders_customer_created
ON orders (customer_id, created_at DESC);
```

Esse índice existe porque uma consulta comum será “listar pedidos de um cliente ordenados por data”.

## 7.2 — Replicação

Replicação cria cópias dos dados em outros nós. Ela pode melhorar leitura, disponibilidade e recuperação.

Cuidados:

- replicação assíncrona pode gerar atraso;
- failover precisa ser testado;
- aplicações precisam saber quando ler do primário;
- réplicas podem não estar atualizadas.

## 7.3 — Sharding

Sharding divide dados em partições horizontais. Cada shard contém parte dos dados.

Critérios para escolher chave:

- alta cardinalidade;
- distribuição uniforme;
- relação com consultas principais;
- baixo risco de hot partition;
- facilidade para roteamento.

Exemplo:

```python
def choose_shard(user_id: int, total_shards: int) -> int:
    return user_id % total_shards
```

Esse exemplo é didático. Em sistemas reais, rebalancing pode ser difícil com módulo simples. Hashing consistente ajuda a reduzir movimentação de dados.

## 7.4 — Hot partition

Hot partition ocorre quando muitos acessos caem no mesmo shard. Pode acontecer com:

- celebridades em rede social;
- produto muito acessado em promoção;
- tenant muito maior que os outros;
- chave baseada em timestamp atual.

Mitigações:

- shard dedicado para grandes tenants;
- particionamento composto;
- salting da chave;
- cache específico;
- filas por partição;
- read replicas.

## 7.5 — Consistência

Consistência define o quão atualizada uma leitura precisa ser.

- **Consistência forte:** após uma escrita, leituras já veem o novo valor.
- **Consistência eventual:** leituras podem ver valor antigo por um período curto.

Exemplo: saldo bancário exige consistência forte. Contador de likes pode aceitar consistência eventual.

## 7.6 — Transações distribuídas

Quando uma operação envolve múltiplos serviços ou bancos, transações ficam mais complexas. Em microservices, uma alternativa comum é usar **sagas**.

Exemplo de saga de pedido:

1. criar pedido pendente;
2. reservar estoque;
3. autorizar pagamento;
4. confirmar pedido;
5. publicar evento.

Se o pagamento falhar, executa compensação:

1. liberar estoque;
2. marcar pedido como cancelado;
3. notificar cliente.

## 7.7 — Resumo do capítulo

A escolha do banco depende dos requisitos e padrões de acesso. Replicação melhora leitura e disponibilidade. Sharding ajuda a lidar com volume, mas aumenta complexidade. Consistência precisa ser pensada por caso de uso.

## 7.8 — Exercícios

1. Modele tabelas para um sistema de posts e comentários.
2. Defina quais consultas precisam de índice.
3. Escolha uma sharding key para um sistema multi-tenant.
4. Liste quais dados precisam de consistência forte em um e-commerce.

---

# Capítulo 8 — Mensageria e comunicação assíncrona

Sistemas grandes não devem processar tudo de forma síncrona. Filas e eventos permitem desacoplar serviços, absorver picos e tornar fluxos mais resilientes.

Ao final deste capítulo, você será capaz de:

- diferenciar comunicação síncrona e assíncrona;
- desenhar produtores, filas e consumidores;
- entender retries, DLQ e idempotência;
- projetar eventos de domínio.

## 8.1 — Comunicação síncrona

Na comunicação síncrona, um serviço chama outro e espera resposta.

Exemplo:

```text
API de Pedido -> Serviço de Estoque -> resposta
API de Pedido -> Serviço de Pagamento -> resposta
API de Pedido -> Serviço de Notificação -> resposta
```

Isso é simples, mas cria acoplamento temporal. Se um serviço lento ou indisponível estiver no meio do fluxo, a requisição inteira pode falhar.

## 8.2 — Comunicação assíncrona

Na comunicação assíncrona, um serviço publica uma mensagem e continua. Consumidores processam depois.

```json
{
  "event_id": "evt_123",
  "event_type": "OrderCreated",
  "occurred_at": "2026-05-31T12:00:00Z",
  "payload": {
    "order_id": "ord_123",
    "customer_id": "cus_456",
    "total_amount": 129.90
  }
}
```

Esse evento pode ser consumido por serviços de pagamento, estoque, notificação e analytics.

## 8.3 — Filas vs tópicos

| Modelo | Como funciona | Uso comum |
|---|---|---|
| Fila | Uma mensagem é processada por um consumidor do grupo | Jobs, processamento em background |
| Tópico/pub-sub | Vários assinantes recebem o mesmo evento | Eventos de domínio, integrações |

## 8.4 — Retry

Falhas temporárias são comuns. O consumidor pode tentar novamente.

Cuidados:

- retries infinitos travam o sistema;
- retries imediatos podem piorar sobrecarga;
- use backoff exponencial;
- defina número máximo de tentativas;
- envie falhas persistentes para DLQ.

## 8.5 — Dead Letter Queue

DLQ é uma fila para mensagens que falharam várias vezes. Ela permite investigar o problema sem bloquear o fluxo principal.

Exemplo de metadados úteis:

```json
{
  "original_event_id": "evt_123",
  "failed_at": "2026-05-31T12:05:00Z",
  "attempts": 5,
  "error": "payment provider timeout"
}
```

## 8.6 — Idempotência no consumidor

Mensagens podem ser entregues mais de uma vez. O consumidor deve tolerar duplicidade.

```python
def handle_order_created(event):
    if processed_events.exists(event["event_id"]):
        return

    process_order(event)
    processed_events.save(event["event_id"])
```

## 8.7 — Resumo do capítulo

Mensageria ajuda a criar sistemas resilientes e escaláveis, mas exige disciplina: eventos bem modelados, retries, DLQ, idempotência, monitoramento de lag e contratos estáveis.

## 8.8 — Exercícios

1. Liste três operações da sua API que poderiam virar jobs assíncronos.
2. Modele um evento `UserRegistered`.
3. Explique como evitar duplicidade ao processar eventos.
4. Defina uma política de retry para envio de e-mail.

---

# Capítulo 9 — Rate limiting

Rate limiting limita a quantidade de requisições que um cliente pode fazer em um período. É essencial para proteger APIs contra abuso, picos inesperados, bugs de clientes e ataques.

Ao final deste capítulo, você será capaz de:

- entender por que rate limiting é necessário;
- comparar algoritmos comuns;
- projetar rate limiting usando Redis;
- retornar headers úteis ao cliente.

## 9.1 — Onde aplicar rate limiting

Rate limiting pode ficar:

- no API Gateway;
- no load balancer;
- em middleware da aplicação;
- em serviço centralizado;
- em proxy reverso.

![Figura 9.1 - Rate limiter como middleware extraído do PDF de referência.](./images/system-design/28_rate_limiter_middleware.png)

*Figura 9.1 - Rate limiter como middleware extraído do PDF de referência.*

![Figura 9.2 - Rate limiter em arquitetura de microservices extraído do PDF de referência.](./images/system-design/29_rate_limiter_microservice.png)

*Figura 9.2 - Rate limiter em arquitetura de microservices extraído do PDF de referência.*

Em APIs backend, normalmente usamos chaves como:

- usuário autenticado;
- IP;
- API key;
- tenant;
- rota;
- combinação de usuário + endpoint.

## 9.2 — Token bucket

![Figura 9.3 - Algoritmo Token Bucket extraído do PDF de referência.](./images/system-design/30_token_bucket.png)

*Figura 9.3 - Algoritmo Token Bucket extraído do PDF de referência.*

![Figura 9.4 - Token Bucket com múltiplos buckets extraído do PDF de referência.](./images/system-design/31_token_bucket_two_buckets.png)

*Figura 9.4 - Token Bucket com múltiplos buckets extraído do PDF de referência.*

No token bucket, o cliente tem um balde com tokens. Cada requisição consome um token. Tokens são repostos ao longo do tempo. Se não houver token, a requisição é negada.

Vantagem: permite rajadas controladas.

## 9.3 — Leaking bucket

![Figura 9.5 - Algoritmo Leaking Bucket extraído do PDF de referência.](./images/system-design/32_leaking_bucket.png)

*Figura 9.5 - Algoritmo Leaking Bucket extraído do PDF de referência.*

No leaking bucket, as requisições entram em uma fila e saem em taxa fixa. Se a fila enche, novas requisições são descartadas.

Vantagem: suaviza tráfego. Desvantagem: pode aumentar latência.

## 9.4 — Fixed window counter

![Figura 9.6 - Fixed Window Counter extraído do PDF de referência.](./images/system-design/33_fixed_window_counter.png)

*Figura 9.6 - Fixed Window Counter extraído do PDF de referência.*

![Figura 9.7 - Problema de borda em janela fixa extraído do PDF de referência.](./images/system-design/34_fixed_window_edge_case.png)

*Figura 9.7 - Problema de borda em janela fixa extraído do PDF de referência.*

Na janela fixa, contamos requisições dentro de intervalos como “por minuto”. É simples, mas permite picos na virada da janela.

## 9.5 — Sliding window

![Figura 9.8 - Sliding Window Log extraído do PDF de referência.](./images/system-design/35_sliding_window_log.png)

*Figura 9.8 - Sliding Window Log extraído do PDF de referência.*

![Figura 9.9 - Sliding Window Counter extraído do PDF de referência.](./images/system-design/36_sliding_window_counter.png)

*Figura 9.9 - Sliding Window Counter extraído do PDF de referência.*

Sliding window evita parte dos problemas da janela fixa. Pode armazenar timestamps ou combinar contadores de janelas anteriores.

## 9.6 — Exemplo com Redis

Um rate limiter simples por janela fixa:

```python
def allow_request(user_id: str, limit: int = 100, window_seconds: int = 60):
    key = f"rate-limit:{user_id}:{current_minute()}"

    count = redis.incr(key)
    if count == 1:
        redis.expire(key, window_seconds)

    return count <= limit
```

Esse exemplo é didático. Em produção, prefira operação atômica com Lua script ou comandos que garantam consistência.

## 9.7 — Headers de resposta

![Figura 9.10 - Headers de resposta em rate limiting extraídos do PDF de referência.](./images/system-design/39_rate_limiter_response_headers.png)

*Figura 9.10 - Headers de resposta em rate limiting extraídos do PDF de referência.*

Headers úteis:

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 42
X-RateLimit-Reset: 1717152000
Retry-After: 30
```

Quando o limite for excedido:

```http
HTTP/1.1 429 Too Many Requests
Retry-After: 30
```

## 9.8 — Arquitetura final

![Figura 9.11 - Design final de rate limiter extraído do PDF de referência.](./images/system-design/40_rate_limiter_final_design.png)

*Figura 9.11 - Design final de rate limiter extraído do PDF de referência.*

## 9.9 — Resumo do capítulo

Rate limiting protege o sistema e melhora previsibilidade. Token bucket é uma escolha flexível para APIs. Redis é comum para armazenar contadores distribuídos, mas é preciso pensar em atomicidade, expiração, alta disponibilidade e observabilidade.

## 9.10 — Exercícios

1. Defina limites para uma API pública de login.
2. Escolha um algoritmo para uma API que precisa aceitar rajadas curtas.
3. Implemente um rate limiter simples usando pseudocódigo.
4. Explique por que `429` é melhor que retornar `500` quando o limite estoura.

---

# Capítulo 10 — Hashing consistente

Hashing consistente é uma técnica usada para distribuir dados ou tráfego entre servidores reduzindo realocação quando nós entram ou saem do cluster.

Ao final deste capítulo, você será capaz de:

- entender o problema do hashing simples;
- explicar o anel de hashing;
- entender virtual nodes;
- reconhecer usos em cache, sharding e balanceamento.

## 10.1 — Problema do módulo simples

Uma distribuição simples poderia ser:

```python
server_index = hash(key) % number_of_servers
```

O problema é que, se o número de servidores muda, muitas chaves mudam de destino. Isso causa muita movimentação de dados.

## 10.2 — Anel de hashing consistente

![Figura 10.1 - Hashing consistente em anel extraído do PDF de referência.](./images/system-design/41_consistent_hashing_basic.png)

*Figura 10.1 - Hashing consistente em anel extraído do PDF de referência.*

No hashing consistente, tanto servidores quanto chaves são posicionados em um anel. Cada chave pertence ao primeiro servidor encontrado no sentido horário.

## 10.3 — Removendo e adicionando servidores

![Figura 10.2 - Remoção de servidor no hashing consistente extraída do PDF de referência.](./images/system-design/42_consistent_hashing_remove_server.png)

*Figura 10.2 - Remoção de servidor no hashing consistente extraída do PDF de referência.*

![Figura 10.3 - Adição de servidor no hashing consistente extraída do PDF de referência.](./images/system-design/43_consistent_hashing_add_server.png)

*Figura 10.3 - Adição de servidor no hashing consistente extraída do PDF de referência.*

Quando um servidor sai, apenas parte das chaves precisa mudar de dono. Quando um servidor entra, ele assume apenas uma fatia do anel.

## 10.4 — Virtual nodes

![Figura 10.4 - Virtual nodes no hashing consistente extraídos do PDF de referência.](./images/system-design/45_consistent_hashing_virtual_nodes.png)

*Figura 10.4 - Virtual nodes no hashing consistente extraídos do PDF de referência.*

![Figura 10.5 - Distribuição final com hashing consistente extraída do PDF de referência.](./images/system-design/47_consistent_hashing_final.png)

*Figura 10.5 - Distribuição final com hashing consistente extraída do PDF de referência.*

Virtual nodes melhoram a distribuição. Em vez de cada servidor aparecer uma vez no anel, ele aparece várias vezes. Isso reduz desequilíbrio.

## 10.5 — Onde usar

Hashing consistente aparece em:

- caches distribuídos;
- bancos particionados;
- sistemas de armazenamento distribuído;
- roteamento de requests por chave;
- balanceamento com afinidade.

## 10.6 — Resumo do capítulo

Hashing consistente reduz o custo de redistribuir dados quando nós entram ou saem. É uma técnica essencial para sistemas distribuídos que precisam particionar dados com estabilidade.

## 10.7 — Exercícios

1. Explique por que `hash(key) % N` é problemático quando N muda.
2. Dê um exemplo de uso de hashing consistente em cache.
3. Explique a função dos virtual nodes.

---

# Capítulo 11 — Key-value store e sistemas distribuídos

Um key-value store é um banco que armazena dados por chave. Ele parece simples, mas construir um key-value store distribuído envolve particionamento, replicação, consistência, resolução de conflitos e tolerância a falhas.

Ao final deste capítulo, você será capaz de:

- entender a API básica de um key-value store;
- reconhecer conceitos de Dynamo-like systems;
- entender quorum;
- entender vector clocks em alto nível;
- visualizar paths de leitura e escrita.

## 11.1 — API básica

![Figura 11.1 - API básica de key-value store extraída do PDF de referência.](./images/system-design/48_key_value_store_api.png)

*Figura 11.1 - API básica de key-value store extraída do PDF de referência.*

A interface mínima costuma ser:

```text
put(key, value)
get(key)
```

Exemplo:

```python
kv.put("user:123", {"name": "Diego"})
user = kv.get("user:123")
```

## 11.2 — Particionamento de dados

![Figura 11.2 - Particionamento de dados em key-value store extraído do PDF de referência.](./images/system-design/49_key_value_data_partition.png)

*Figura 11.2 - Particionamento de dados em key-value store extraído do PDF de referência.*

As chaves são distribuídas entre nós. Hashing consistente pode ser usado para decidir onde cada chave vive.

## 11.3 — Replicação e quorum

Em sistemas distribuídos, um dado pode ser replicado em N nós. Leitura e escrita podem exigir confirmação de R e W nós.

```text
N = número de réplicas
W = número de confirmações para escrita
R = número de confirmações para leitura
```

Uma regra comum para consistência forte é:

```text
R + W > N
```

Exemplo:

```text
N = 3
W = 2
R = 2
R + W = 4 > 3
```

Isso aumenta chance de que leitura veja a escrita mais recente.

## 11.4 — Versionamento e conflitos

![Figura 11.3 - Versionamento e vector clock extraídos do PDF de referência.](./images/system-design/51_data_versioning_vector_clock.png)

*Figura 11.3 - Versionamento e vector clock extraídos do PDF de referência.*

Em sistemas distribuídos, duas escritas podem acontecer em paralelo. O sistema precisa detectar conflito e resolver.

Estratégias:

- last-write-wins;
- vector clocks;
- merge pela aplicação;
- CRDTs em casos específicos.

## 11.5 — Caminho de escrita e leitura

![Figura 11.4 - Caminho de escrita em key-value store extraído do PDF de referência.](./images/system-design/52_key_value_write_path.png)

*Figura 11.4 - Caminho de escrita em key-value store extraído do PDF de referência.*

![Figura 11.5 - Caminho de leitura em key-value store extraído do PDF de referência.](./images/system-design/53_key_value_read_path.png)

*Figura 11.5 - Caminho de leitura em key-value store extraído do PDF de referência.*

Uma escrita pode passar por commit log, memtable e SSTables. Uma leitura pode consultar memtable, cache, bloom filters e arquivos persistidos.

Como backend, você não precisa implementar isso do zero na maior parte dos projetos, mas entender o fluxo ajuda a compreender por que bancos distribuídos têm latência, compactação, consistência eventual e tuning.

## 11.6 — Arquitetura final

![Figura 11.6 - Arquitetura de key-value store distribuído extraída do PDF de referência.](./images/system-design/54_key_value_store_final_architecture.png)

*Figura 11.6 - Arquitetura de key-value store distribuído extraída do PDF de referência.*

## 11.7 — Resumo do capítulo

Key-value stores são simples na API, mas complexos internamente. Eles são úteis para alto volume, baixa latência e acesso por chave. Em troca, você precisa entender particionamento, replicação, conflitos e consistência.

## 11.8 — Exercícios

1. Dê três exemplos de dados que cabem bem em key-value store.
2. Explique o significado de N, R e W.
3. O que pode acontecer se duas regiões atualizam a mesma chave ao mesmo tempo?

---

# Capítulo 12 — Geração de IDs e encurtador de URLs

IDs únicos aparecem em quase todo backend: pedidos, pagamentos, usuários, eventos, logs, mensagens e arquivos. Em sistemas distribuídos, gerar IDs únicos com ordem, baixa latência e sem coordenação central é um desafio.

Ao final deste capítulo, você será capaz de:

- comparar estratégias de geração de ID;
- entender o formato Snowflake;
- projetar um encurtador de URLs;
- evitar colisões e gargalos.

## 12.1 — Estratégias para IDs únicos

Opções comuns:

| Estratégia | Vantagem | Cuidado |
|---|---|---|
| Auto incremento no banco | Simples | Gargalo central e difícil em múltiplas regiões |
| UUID | Distribuído e fácil | Grande e não ordenado por padrão |
| Snowflake | Ordenável e distribuído | Depende de relógio e configuração de workers |
| Segment allocation | Reduz chamadas ao banco | Pode desperdiçar IDs |

## 12.2 — UUID

![Figura 12.1 - Uso de UUID para geração de IDs extraído do PDF de referência.](./images/system-design/56_unique_id_uuid.png)

*Figura 12.1 - Uso de UUID para geração de IDs extraído do PDF de referência.*

UUID é prático quando você precisa de unicidade sem coordenação central. Em bancos relacionais, pode ter custo maior de índice, especialmente se os valores forem aleatórios.

## 12.3 — Snowflake

![Figura 12.2 - Layout de ID no estilo Snowflake extraído do PDF de referência.](./images/system-design/57_snowflake_layout.png)

*Figura 12.2 - Layout de ID no estilo Snowflake extraído do PDF de referência.*

![Figura 12.3 - Geração de ID distribuído extraída do PDF de referência.](./images/system-design/58_snowflake_id_generator.png)

*Figura 12.3 - Geração de ID distribuído extraída do PDF de referência.*

Um ID estilo Snowflake normalmente combina:

- timestamp;
- ID do data center;
- ID da máquina/worker;
- número sequencial.

Isso permite gerar IDs ordenáveis por tempo e sem coordenação a cada requisição.

## 12.4 — Encurtador de URLs

Um encurtador transforma uma URL longa em um código curto.

![Figura 12.4 - Fluxo básico de encurtador de URLs extraído do PDF de referência.](./images/system-design/59_url_shortener_flow.png)

*Figura 12.4 - Fluxo básico de encurtador de URLs extraído do PDF de referência.*

APIs principais:

```http
POST /short-urls
GET /{short_code}
```

Modelo simples:

```sql
CREATE TABLE urls (
    id BIGSERIAL PRIMARY KEY,
    short_code VARCHAR(20) UNIQUE NOT NULL,
    original_url TEXT NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT now(),
    expires_at TIMESTAMP
);
```

## 12.5 — Base62

Base62 usa letras maiúsculas, minúsculas e números.

```text
0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ
```

Trecho didático:

```python
ALPHABET = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ"

def encode_base62(number: int) -> str:
    if number == 0:
        return ALPHABET[0]

    result = []
    base = len(ALPHABET)

    while number > 0:
        number, remainder = divmod(number, base)
        result.append(ALPHABET[remainder])

    return "".join(reversed(result))
```

## 12.6 — Design final do encurtador

![Figura 12.5 - Design de alto nível de encurtador de URLs extraído do PDF de referência.](./images/system-design/63_url_shortener_final_design.png)

*Figura 12.5 - Design de alto nível de encurtador de URLs extraído do PDF de referência.*

O caminho de leitura precisa ser extremamente rápido:

1. cliente acessa código curto;
2. API consulta cache;
3. se não encontrar, consulta banco;
4. retorna redirect `301` ou `302`;
5. registra evento de analytics de forma assíncrona.

## 12.7 — Resumo do capítulo

IDs distribuídos precisam equilibrar unicidade, ordenação, tamanho e facilidade operacional. Encurtadores de URL parecem simples, mas exigem boas decisões sobre geração de código, cache, redirecionamento, expiração e analytics.

## 12.8 — Exercícios

1. Compare UUID e Snowflake para IDs de pedidos.
2. Modele a tabela de analytics de um encurtador.
3. Explique por que o redirecionamento deveria usar cache.
4. O que acontece se dois códigos curtos colidirem?

---

# Capítulo 13 — Notificações, chat e presença online

Sistemas de comunicação exigem baixa latência, confiabilidade e integração com múltiplos canais. Notificações e chat são bons exemplos para backend porque combinam APIs, filas, WebSocket, estado de presença e persistência.

Ao final deste capítulo, você será capaz de:

- desenhar um sistema de notificações;
- entender WebSocket;
- modelar mensagens de chat;
- entender presença online.

## 13.1 — Sistema de notificações

![Figura 13.1 - Sistema básico de notificações extraído do PDF de referência.](./images/system-design/64_notification_system_basic.png)

*Figura 13.1 - Sistema básico de notificações extraído do PDF de referência.*

![Figura 13.2 - Notificações com filas e workers extraídas do PDF de referência.](./images/system-design/66_notification_system_queue_workers.png)

*Figura 13.2 - Notificações com filas e workers extraídas do PDF de referência.*

Um sistema de notificações pode enviar:

- push notification;
- e-mail;
- SMS;
- webhook;
- mensagem interna.

Fluxo recomendado:

1. serviço de origem publica evento;
2. serviço de notificação recebe;
3. aplica preferências do usuário;
4. escolhe canal;
5. envia para provider;
6. registra status;
7. faz retry em falhas temporárias.

## 13.2 — Modelo de dados de notificação

```sql
CREATE TABLE notifications (
    id UUID PRIMARY KEY,
    user_id UUID NOT NULL,
    channel TEXT NOT NULL,
    title TEXT NOT NULL,
    body TEXT NOT NULL,
    status TEXT NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT now(),
    sent_at TIMESTAMP
);
```

## 13.3 — Design final de notificações

![Figura 13.3 - Design final de sistema de notificações extraído do PDF de referência.](./images/system-design/67_notification_system_final_design.png)

*Figura 13.3 - Design final de sistema de notificações extraído do PDF de referência.*

## 13.4 — Chat: polling, long polling e WebSocket

![Figura 13.4 - Sistema básico de chat extraído do PDF de referência.](./images/system-design/68_chat_system_basic.png)

*Figura 13.4 - Sistema básico de chat extraído do PDF de referência.*

![Figura 13.5 - Comunicação via WebSocket extraída do PDF de referência.](./images/system-design/69_chat_websocket.png)

*Figura 13.5 - Comunicação via WebSocket extraída do PDF de referência.*

![Figura 13.6 - Comparação de protocolos para chat extraída do PDF de referência.](./images/system-design/70_chat_protocol_comparison.png)

*Figura 13.6 - Comparação de protocolos para chat extraída do PDF de referência.*

Para chat em tempo real, WebSocket é uma escolha comum porque mantém conexão aberta e permite comunicação bidirecional.

Fluxo de envio:

1. cliente envia mensagem via WebSocket;
2. servidor valida usuário e conversa;
3. mensagem é persistida;
4. evento é publicado;
5. destinatários conectados recebem em tempo real;
6. usuários offline recebem push notification.

## 13.5 — Fluxo de mensagens

![Figura 13.7 - Fluxo de mensagens em sistema de chat extraído do PDF de referência.](./images/system-design/71_chat_message_flow.png)

*Figura 13.7 - Fluxo de mensagens em sistema de chat extraído do PDF de referência.*

Modelo simplificado:

```sql
CREATE TABLE chat_messages (
    id UUID PRIMARY KEY,
    conversation_id UUID NOT NULL,
    sender_id UUID NOT NULL,
    body TEXT NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT now()
);
```

## 13.6 — Presença online

![Figura 13.8 - Presença online em chat extraída do PDF de referência.](./images/system-design/72_chat_online_presence.png)

*Figura 13.8 - Presença online em chat extraída do PDF de referência.*

Presença online não precisa necessariamente ser 100% consistente. Uma abordagem comum:

- conexão WebSocket registra usuário como online;
- heartbeat renova estado;
- TTL remove usuário se conexão morrer;
- status é publicado para contatos.

## 13.7 — Design final do chat

![Figura 13.9 - Design final de sistema de chat extraído do PDF de referência.](./images/system-design/73_chat_final_design.png)

*Figura 13.9 - Design final de sistema de chat extraído do PDF de referência.*

## 13.8 — Resumo do capítulo

Notificações e chat combinam APIs, eventos, filas, WebSocket, armazenamento e estado temporário. O backend precisa lidar com duplicidade, usuários offline, retries, ordenação de mensagens e observabilidade.

## 13.9 — Exercícios

1. Modele uma notificação de pedido aprovado.
2. Defina uma política de retry para envio de SMS.
3. Desenhe o fluxo de envio de mensagem em chat 1:1.
4. Explique como detectar usuário offline.

---

# Capítulo 14 — Busca, autocomplete e crawling

Busca e autocomplete são sistemas de leitura intensiva. Eles exigem indexação, ranking, atualização incremental e baixa latência.

Ao final deste capítulo, você será capaz de:

- entender trie para autocomplete;
- diferenciar banco transacional de índice de busca;
- desenhar pipeline de coleta e indexação;
- entender crawling em alto nível.

## 14.1 — Autocomplete

![Figura 14.1 - Sistema básico de autocomplete extraído do PDF de referência.](./images/system-design/74_autocomplete_basic.png)

*Figura 14.1 - Sistema básico de autocomplete extraído do PDF de referência.*

![Figura 14.2 - Trie para autocomplete extraída do PDF de referência.](./images/system-design/75_autocomplete_trie.png)

*Figura 14.2 - Trie para autocomplete extraída do PDF de referência.*

Autocomplete precisa responder rápido enquanto o usuário digita. Uma estrutura comum é trie, onde prefixos compartilham nós.

Exemplo conceitual:

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.suggestions = []
```

Em sistemas reais, sugestões são pré-computadas, ranqueadas por popularidade, idioma, localização e histórico.

## 14.2 — Pipeline de dados para autocomplete

![Figura 14.3 - Serviço de coleta de dados para autocomplete extraído do PDF de referência.](./images/system-design/76_autocomplete_data_gathering_service.png)

*Figura 14.3 - Serviço de coleta de dados para autocomplete extraído do PDF de referência.*

![Figura 14.4 - Design final de autocomplete extraído do PDF de referência.](./images/system-design/77_autocomplete_final_design.png)

*Figura 14.4 - Design final de autocomplete extraído do PDF de referência.*

Uma arquitetura comum:

1. usuários pesquisam termos;
2. logs de busca são coletados;
3. jobs agregam popularidade;
4. índice de sugestões é atualizado;
5. API de autocomplete consulta estrutura otimizada.

## 14.3 — Busca textual

Para busca textual completa, normalmente usamos motores como Elasticsearch, OpenSearch, Solr ou soluções gerenciadas. O banco transacional continua sendo fonte de verdade, enquanto o índice de busca é otimizado para consulta.

Fluxo comum:

1. dado é salvo no banco;
2. evento é publicado;
3. worker atualiza índice de busca;
4. API consulta índice;
5. detalhes finais podem ser buscados no banco.

## 14.4 — Web crawler

Um crawler coleta páginas ou documentos seguindo links e regras. Componentes comuns:

- URL frontier;
- downloader;
- parser;
- deduplicação;
- armazenamento de conteúdo;
- scheduler;
- rate limit por domínio;
- robots.txt;
- pipeline de indexação.

## 14.5 — Resumo do capítulo

Busca e autocomplete normalmente exigem dados pré-processados. Não tente resolver tudo com consultas diretas no banco transacional. Use pipelines, índices e caches apropriados.

## 14.6 — Exercícios

1. Modele um autocomplete de produtos.
2. Explique por que autocomplete precisa de baixa latência.
3. Defina eventos para atualizar um índice de busca.
4. Liste cuidados de um crawler para não sobrecarregar sites externos.

---

# Capítulo 15 — Streaming, arquivos e sincronização

Sistemas de vídeo e arquivos são úteis para aprender sobre upload, processamento assíncrono, object storage, CDN, metadados, sincronização e conflitos.

Ao final deste capítulo, você será capaz de:

- entender arquitetura de upload e streaming;
- separar metadados de arquivos;
- usar object storage e CDN;
- entender sincronização de arquivos e conflitos.

## 15.1 — Sistema de vídeo

![Figura 15.1 - Fluxo de upload e streaming de vídeo extraído do PDF de referência.](./images/system-design/79_youtube_upload_streaming.png)

*Figura 15.1 - Fluxo de upload e streaming de vídeo extraído do PDF de referência.*

![Figura 15.2 - Design de alto nível de sistema de vídeo extraído do PDF de referência.](./images/system-design/80_youtube_high_level_design.png)

*Figura 15.2 - Design de alto nível de sistema de vídeo extraído do PDF de referência.*

Um sistema de vídeo geralmente tem:

- API para metadados;
- upload direto para object storage;
- fila de processamento;
- workers de transcodificação;
- armazenamento de vídeos processados;
- CDN;
- banco de metadados;
- serviço de recomendação e busca.

## 15.2 — Transcodificação

![Figura 15.3 - Pipeline de transcodificação extraído do PDF de referência.](./images/system-design/81_youtube_video_transcoding.png)

*Figura 15.3 - Pipeline de transcodificação extraído do PDF de referência.*

Transcodificação converte o vídeo para diferentes resoluções, formatos e bitrates. É trabalho pesado e deve ser assíncrono.

Fluxo:

1. vídeo original é enviado;
2. evento `VideoUploaded` é publicado;
3. workers processam versões;
4. metadados são atualizados;
5. CDN passa a entregar versões processadas.

## 15.3 — Design final de vídeo

![Figura 15.4 - Design final de sistema de vídeo extraído do PDF de referência.](./images/system-design/82_youtube_final_design.png)

*Figura 15.4 - Design final de sistema de vídeo extraído do PDF de referência.*

## 15.4 — Sistema de arquivos tipo Drive

![Figura 15.5 - Fluxo básico de armazenamento de arquivos extraído do PDF de referência.](./images/system-design/83_google_drive_flow.png)

*Figura 15.5 - Fluxo básico de armazenamento de arquivos extraído do PDF de referência.*

![Figura 15.6 - Design de alto nível de sistema de arquivos extraído do PDF de referência.](./images/system-design/84_google_drive_high_level_design.png)

*Figura 15.6 - Design de alto nível de sistema de arquivos extraído do PDF de referência.*

Um sistema de arquivos precisa lidar com:

- upload;
- download;
- metadados;
- permissões;
- versões;
- sincronização;
- conflitos;
- notificações de mudança.

Separe:

- **conteúdo binário:** object storage;
- **metadados:** banco relacional ou NoSQL;
- **eventos:** fila/pub-sub;
- **entrega:** CDN;
- **sincronização:** serviço específico.

## 15.5 — Conflitos de sincronização

![Figura 15.7 - Conflito de sincronização extraído do PDF de referência.](./images/system-design/85_google_drive_sync_conflict.png)

*Figura 15.7 - Conflito de sincronização extraído do PDF de referência.*

Conflitos aparecem quando dois clientes alteram o mesmo arquivo offline ou em paralelo. Estratégias:

- manter versões;
- criar cópia conflitante;
- merge quando possível;
- usar last-write-wins apenas quando aceitável;
- pedir resolução manual.

## 15.6 — Notificações de mudança

![Figura 15.8 - Serviço de notificação de mudanças extraído do PDF de referência.](./images/system-design/86_google_drive_notification_service.png)

*Figura 15.8 - Serviço de notificação de mudanças extraído do PDF de referência.*

![Figura 15.9 - Design final de sistema de arquivos extraído do PDF de referência.](./images/system-design/87_google_drive_final_design.png)

*Figura 15.9 - Design final de sistema de arquivos extraído do PDF de referência.*

## 15.7 — Resumo do capítulo

Sistemas de mídia e arquivos exigem separação clara entre metadados, binários, processamento e entrega. Object storage, filas, workers e CDN são componentes centrais.

## 15.8 — Exercícios

1. Desenhe o fluxo de upload de imagem em uma rede social.
2. Explique por que processamento de vídeo não deve acontecer dentro da requisição HTTP.
3. Modele metadados de arquivo.
4. Proponha uma estratégia para conflito de edição offline.

---

# Capítulo 16 — Observabilidade, confiabilidade e operação

Um sistema só é realmente útil se puder ser operado. Observabilidade e confiabilidade são partes do design, não tarefas finais.

Ao final deste capítulo, você será capaz de:

- diferenciar logs, métricas e traces;
- definir health checks;
- entender SLOs;
- projetar retries, timeouts e circuit breakers;
- pensar em deploy e rollback.

## 16.1 — Logs

Logs registram eventos importantes do sistema. Bons logs devem ter contexto:

```json
{
  "level": "error",
  "message": "payment provider timeout",
  "order_id": "ord_123",
  "customer_id": "cus_456",
  "trace_id": "trc_789",
  "timestamp": "2026-05-31T12:00:00Z"
}
```

Evite logs sem contexto e nunca registre senhas, tokens ou dados sensíveis.

## 16.2 — Métricas

Métricas são números agregáveis:

- QPS;
- latência p50/p95/p99;
- taxa de erro;
- CPU;
- memória;
- conexões;
- tamanho da fila;
- cache hit ratio;
- throughput de workers.

## 16.3 — Tracing distribuído

Tracing conecta chamadas entre serviços. É útil quando uma requisição passa por API Gateway, serviço A, serviço B, banco, cache e fila.

Use `trace_id` e `span_id` para seguir o caminho da requisição.

## 16.4 — Quatro sinais dourados

Os quatro sinais úteis para serviços voltados ao usuário são:

- **latência:** quanto tempo leva para responder;
- **tráfego:** quanto o sistema está recebendo;
- **erros:** quantas requisições falham;
- **saturação:** quão perto o sistema está do limite.

## 16.5 — Health checks

Health checks indicam se uma instância está pronta ou viva.

```http
GET /health/live
GET /health/ready
```

- **liveness:** o processo está vivo?
- **readiness:** a instância está pronta para receber tráfego?

Readiness pode verificar banco, cache, migrations e dependências essenciais.

## 16.6 — Timeouts

Sem timeout, uma chamada pode ficar presa por tempo indeterminado.

```python
response = http_client.get(
    "https://payment-provider.example.com/status",
    timeout=2.0,
)
```

Defina timeouts por dependência. Um timeout muito longo consome threads/conexões. Um timeout muito curto gera falhas falsas.

## 16.7 — Retry com backoff

Retries ajudam em falhas temporárias, mas podem piorar uma sobrecarga.

```python
for attempt in range(1, 4):
    try:
        return call_external_service()
    except TimeoutError:
        sleep(2 ** attempt)
```

Use backoff, jitter e limite de tentativas.

## 16.8 — Circuit breaker

Circuit breaker interrompe chamadas para uma dependência instável. Estados comuns:

- **closed:** chamadas passam normalmente;
- **open:** chamadas são bloqueadas temporariamente;
- **half-open:** algumas chamadas testam recuperação.

## 16.9 — SLO, SLA e erro aceitável

- **SLA:** compromisso formal com cliente.
- **SLO:** objetivo interno de nível de serviço.
- **SLI:** métrica usada para medir serviço.

Exemplo:

```text
SLI: percentual de requisições HTTP 2xx/3xx em /checkout
SLO: 99,9% das requisições de checkout com sucesso por mês
SLA: contrato com penalidade se ficar abaixo de 99,5%
```

## 16.10 — Deploy e rollback

Estratégias comuns:

- rolling deployment;
- blue/green deployment;
- canary release;
- feature flags;
- rollback automático.

Para backend, todo deploy deve considerar migrations, compatibilidade de schema e versionamento de contratos.

## 16.11 — Resumo do capítulo

Confiabilidade precisa ser desenhada. Logs, métricas, traces, health checks, timeouts, retries, circuit breakers e deploy seguro são tão importantes quanto banco e API.

## 16.12 — Exercícios

1. Defina métricas para uma API de login.
2. Crie um health check para uma aplicação com banco e Redis.
3. Explique quando retry pode piorar o problema.
4. Defina um SLO para endpoint de checkout.

---

# Capítulo 17 — Aplicando System Design em projetos backend

Este capítulo transforma conceitos em prática. A ideia é mostrar como você pode aplicar System Design em projetos Python/FastAPI, Node.js, Go ou qualquer backend moderno.

Ao final deste capítulo, você será capaz de:

- estruturar uma API pensando em evolução;
- usar Docker e Compose como ambiente local;
- planejar CI para validar qualidade;
- documentar decisões arquiteturais.

## 17.1 — Estrutura de projeto backend

Exemplo genérico:

```text
app/
├── api/
│   ├── routes/
│   └── dependencies.py
├── core/
│   ├── config.py
│   └── security.py
├── domain/
│   ├── entities.py
│   └── services.py
├── infrastructure/
│   ├── database.py
│   ├── cache.py
│   └── queue.py
├── workers/
├── tests/
└── main.py
```

A separação evita que regra de negócio fique presa em framework, ORM ou infraestrutura.

## 17.2 — Exemplo de endpoint com processamento assíncrono

```python
@app.post("/reports")
def create_report(payload: ReportRequest, current_user: User = Depends(authenticate)):
    report = report_service.create_pending_report(
        user_id=current_user.id,
        filters=payload.filters,
    )

    queue.publish("ReportRequested", {
        "report_id": str(report.id),
        "user_id": str(current_user.id),
    })

    return {
        "report_id": str(report.id),
        "status": "processing"
    }
```

A API responde rápido e o worker gera o relatório depois.

## 17.3 — Docker Compose local

Ambiente local típico:

```yaml
services:
  api:
    build: .
    ports:
      - "8000:8000"
    environment:
      DATABASE_URL: postgresql://app:app@postgres:5432/app
      REDIS_URL: redis://redis:6379/0
    depends_on:
      - postgres
      - redis

  worker:
    build: .
    command: python -m app.workers.reports
    environment:
      DATABASE_URL: postgresql://app:app@postgres:5432/app
      REDIS_URL: redis://redis:6379/0
    depends_on:
      - postgres
      - redis

  postgres:
    image: postgres:16
    environment:
      POSTGRES_USER: app
      POSTGRES_PASSWORD: app
      POSTGRES_DB: app
    volumes:
      - pgdata:/var/lib/postgresql/data

  redis:
    image: redis:7

volumes:
  pgdata:
```

## 17.4 — CI mínima para backend

Uma pipeline de CI deve rodar:

- lint;
- testes unitários;
- testes de integração;
- build Docker;
- análise de segurança quando possível;
- validação de migrations.

Exemplo GitHub Actions simplificado:

```yaml
name: CI

on:
  pull_request:
  push:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    services:
      postgres:
        image: postgres:16
        env:
          POSTGRES_USER: app
          POSTGRES_PASSWORD: app
          POSTGRES_DB: app
        ports:
          - 5432:5432
        options: >-
          --health-cmd="pg_isready -U app"
          --health-interval=10s
          --health-timeout=5s
          --health-retries=5

      redis:
        image: redis:7
        ports:
          - 6379:6379

    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        env:
          DATABASE_URL: postgresql://app:app@localhost:5432/app
          REDIS_URL: redis://localhost:6379/0
        run: pytest -q
```

## 17.5 — ADR: registrando decisões

ADR significa Architecture Decision Record. É um documento curto para registrar decisões.

Exemplo:

```md
# ADR 001 - Usar Redis para cache de catálogo

## Contexto
A API de catálogo recebe muitas leituras repetidas e o banco está com alta carga.

## Decisão
Usaremos Redis com cache-aside e TTL de 5 minutos para detalhes de produto.

## Consequências
Reduz carga no banco, mas pode retornar dados desatualizados por alguns minutos.
```

## 17.6 — Checklist antes de subir produção

- Existe health check?
- Logs têm `trace_id`?
- Métricas de latência e erro foram configuradas?
- Banco tem índices necessários?
- Variáveis sensíveis estão fora do código?
- Migrations são compatíveis com versão anterior?
- Existe rollback?
- Existe limite de requisições?
- Existe timeout para chamadas externas?
- Filas têm DLQ?
- Workers são idempotentes?

## 17.7 — Resumo do capítulo

System Design aparece no código, no Docker, no CI, no banco, nos logs e na forma como você evolui a aplicação. A meta é construir backend com contratos claros, falhas controladas e evolução segura.

## 17.8 — Exercícios

1. Crie um ADR para escolher PostgreSQL em um projeto.
2. Adicione Redis ao Docker Compose de uma API.
3. Defina um fluxo assíncrono para envio de e-mail.
4. Monte uma checklist de produção para seu projeto atual.

---

# Capítulo 18 — Cheat sheet de System Design

## 18.1 — Processo de design

```text
1. Entenda o problema
2. Liste requisitos funcionais
3. Liste requisitos não funcionais
4. Faça estimativas
5. Desenhe APIs
6. Modele dados
7. Desenhe arquitetura inicial
8. Identifique gargalos
9. Aprofunde componentes críticos
10. Explique trade-offs
```

## 18.2 — Perguntas essenciais

```text
Quem usa?
Quantos usuários?
Qual QPS?
Leitura ou escrita?
Qual latência aceitável?
Qual disponibilidade esperada?
Quais dados são críticos?
Pode ter consistência eventual?
O que acontece se uma dependência cair?
Como monitorar?
Como fazer rollback?
```

## 18.3 — Componentes comuns

| Componente | Quando usar |
|---|---|
| Load balancer | Distribuir tráfego entre instâncias |
| API Gateway | Centralizar entrada, auth, roteamento e limites |
| Cache | Reduzir latência e carga do banco |
| CDN | Entregar estáticos perto do usuário |
| Fila | Processar tarefas assíncronas |
| Worker | Consumir jobs em background |
| Banco relacional | Transações e dados relacionais |
| NoSQL | Escala, baixa latência ou modelo flexível |
| Object storage | Arquivos grandes e binários |
| Search engine | Busca textual e ranking |
| Observabilidade | Operar e debugar produção |

## 18.4 — Decisões rápidas

```text
Precisa responder na hora? Use síncrono.
Pode processar depois? Use fila.
Dado muda pouco e lê muito? Use cache.
Arquivo grande? Use object storage + CDN.
Muitas leituras no banco? Use réplica e cache.
Banco grande demais? Considere sharding.
Muitos serviços? Use API Gateway e observabilidade.
Requisições abusivas? Use rate limiting.
Operação crítica repetida? Use idempotência.
```

## 18.5 — Padrões de resiliência

```text
Timeout
Retry com backoff
Circuit breaker
Bulkhead
Rate limiting
Fallback
Cache
DLQ
Health check
Failover
```

## 18.6 — Métricas importantes

```text
QPS
Latência p50/p95/p99
Taxa de erro
Uso de CPU
Uso de memória
Conexões ativas
Tamanho da fila
Tempo de processamento de workers
Cache hit ratio
Taxa de retry
Tempo de deploy
Tempo de rollback
```

## 18.7 — Fórmulas úteis

```text
QPS médio = operações por dia / 86.400
Pico ≈ QPS médio * fator de pico
Armazenamento diário = quantidade de itens * tamanho médio
Armazenamento total = armazenamento diário * retenção
Instâncias = pico esperado / capacidade por instância
Disponibilidade = tempo operacional / tempo total
```

## 18.8 — Erros comuns

- começar por microservices sem necessidade;
- ignorar requisitos não funcionais;
- não estimar volume;
- esquecer idempotência;
- não configurar timeout;
- usar cache sem estratégia de invalidação;
- criar fila sem DLQ;
- não monitorar lag de fila;
- criar sharding cedo demais;
- não documentar trade-offs;
- desenhar arquitetura sem pensar em operação.

---

# Referências bibliográficas

- XU, Alex. **System Design Interview: An Insider's Guide**. 2. ed. Material em PDF fornecido como referência pelo usuário.
- DONNE MARTIN. **System Design Primer**. Disponível em: <https://github.com/donnemartin/system-design-primer>. Acesso em: 31 maio 2026.
- FOWLER, Martin; LEWIS, James. **Microservices**. Disponível em: <https://martinfowler.com/articles/microservices.html>. Acesso em: 31 maio 2026.
- FOWLER, Martin. **Monolith First**. Disponível em: <https://martinfowler.com/bliki/MonolithFirst.html>. Acesso em: 31 maio 2026.
- GOOGLE SRE. **Monitoring Distributed Systems**. Disponível em: <https://sre.google/sre-book/monitoring-distributed-systems/>. Acesso em: 31 maio 2026.
- AWS. **Well-Architected Framework: Reliability Pillar**. Disponível em: <https://docs.aws.amazon.com/wellarchitected/latest/reliability-pillar/welcome.html>. Acesso em: 31 maio 2026.
- DOCKER. **Docker Documentation**. Disponível em: <https://docs.docker.com/>. Acesso em: 31 maio 2026.
- KUBERNETES. **Kubernetes Documentation**. Disponível em: <https://kubernetes.io/docs/>. Acesso em: 31 maio 2026.
- POSTGRESQL. **PostgreSQL Documentation**. Disponível em: <https://www.postgresql.org/docs/>. Acesso em: 31 maio 2026.
- REDIS. **Redis Documentation**. Disponível em: <https://redis.io/docs/latest/>. Acesso em: 31 maio 2026.
- RABBITMQ. **RabbitMQ Documentation**. Disponível em: <https://www.rabbitmq.com/docs>. Acesso em: 31 maio 2026.
- NGINX. **NGINX Reverse Proxy and Load Balancing Documentation**. Disponível em: <https://docs.nginx.com/>. Acesso em: 31 maio 2026.
