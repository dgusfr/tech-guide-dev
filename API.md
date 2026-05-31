# API — Apostila Didática

> Material refatorado para estudo, revisão e publicação em GitHub.
>
> **Pasta de imagens esperada:** `./images/api/`
>
> As imagens citadas nesta apostila devem ser salvas nessa pasta com os mesmos nomes usados nos links Markdown.

---

## Sobre esta apostila

Esta apostila apresenta os principais conceitos sobre APIs de maneira prática e progressiva. A ideia não é decorar siglas, mas entender como sistemas conversam, como uma requisição HTTP é formada, como uma API REST costuma ser organizada, como testar endpoints, como autenticação funciona e como escolher padrões de integração como polling, webhooks, GraphQL, gRPC e WebSocket.

O conteúdo foi organizado para quem estuda desenvolvimento backend, frontend, integração de sistemas ou qualquer área que precise consumir, testar ou construir APIs.

---

## Como estudar por esta apostila

Leia os capítulos na ordem. Sempre que aparecer um exemplo de código, tente executá-lo ou reescrevê-lo com pequenas alterações. Uma boa forma de fixar o conteúdo é explicar cada endpoint em voz alta: qual recurso ele acessa, qual método HTTP usa, quais dados envia, qual resposta espera e quais erros podem acontecer.

Os trechos de código são curtos de propósito. Eles existem para demonstrar conceitos, não para formar uma aplicação completa. Depois de entender o conceito, você pode aplicar a ideia em uma API real usando uma linguagem ou framework de sua preferência.

---

## Índice

1. [Capítulo 1 — O que é uma API](#capítulo-1--o-que-é-uma-api)
2. [Capítulo 2 — HTTP, requisições e respostas](#capítulo-2--http-requisições-e-respostas)
3. [Capítulo 3 — REST e APIs RESTful](#capítulo-3--rest-e-apis-restful)
4. [Capítulo 4 — Parâmetros, headers, body e cookies](#capítulo-4--parâmetros-headers-body-e-cookies)
5. [Capítulo 5 — JSON em APIs](#capítulo-5--json-em-apis)
6. [Capítulo 6 — Estilos e protocolos de API](#capítulo-6--estilos-e-protocolos-de-api)
7. [Capítulo 7 — Testando APIs com cURL](#capítulo-7--testando-apis-com-curl)
8. [Capítulo 8 — CORS](#capítulo-8--cors)
9. [Capítulo 9 — Autenticação e autorização em APIs](#capítulo-9--autenticação-e-autorização-em-apis)
10. [Capítulo 10 — Padrões de integração: polling, webhooks e pushing](#capítulo-10--padrões-de-integração-polling-webhooks-e-pushing)
11. [Capítulo 11 — Boas práticas para APIs profissionais](#capítulo-11--boas-práticas-para-apis-profissionais)
12. [Referências bibliográficas](#referências-bibliográficas)

---

# Capítulo 1 — O que é uma API

Antes de estudar REST, JSON, autenticação ou WebSocket, é importante entender a ideia central: uma API é uma forma padronizada de comunicação entre sistemas.

Ao final deste capítulo, você será capaz de:

- explicar o que é uma API;
- diferenciar cliente e servidor;
- entender por que APIs são usadas em sistemas reais;
- reconhecer exemplos de APIs no dia a dia.

---

## 1.1 — O problema

Imagine um aplicativo de celular que mostra o saldo de uma conta bancária. O saldo real não está salvo no celular do usuário. Ele fica em algum sistema do banco, protegido em servidores. Então o aplicativo precisa perguntar ao backend do banco: “qual é o saldo deste cliente?”.

Essa comunicação não pode ser feita de qualquer jeito. O aplicativo precisa saber para qual endereço enviar a requisição, qual método usar, quais dados informar, como se autenticar e em qual formato receber a resposta. É justamente para resolver esse tipo de comunicação que usamos APIs.

---

## 1.2 — O que é API?

API significa **Application Programming Interface**, ou **Interface de Programação de Aplicações**. Em termos simples, uma API é um conjunto de regras que permite que um sistema use funcionalidades ou dados de outro sistema.

Uma API define um contrato. Esse contrato informa o que pode ser solicitado, como a solicitação deve ser feita e qual tipo de resposta será devolvida. Quando uma API é bem projetada, o cliente não precisa conhecer o banco de dados, a linguagem ou a estrutura interna do servidor. Ele só precisa seguir o contrato público da API.

Um exemplo simples: quando um frontend precisa listar usuários, ele pode chamar um endpoint como `GET /usuarios`. O backend processa a requisição, consulta os dados necessários e devolve uma resposta, geralmente em JSON.

---

## 1.3 — Modelo cliente-servidor

No modelo cliente-servidor, existem dois papéis principais:

| Papel | Responsabilidade |
|---|---|
| Cliente | Faz a solicitação. Pode ser um navegador, aplicativo mobile, outro backend ou ferramenta de teste. |
| Servidor | Recebe a solicitação, processa a regra de negócio e devolve uma resposta. |

![Diagrama do modelo cliente-servidor](./images/api/clientserverside.png)

*Figura 1 — Representação do modelo cliente-servidor. Fonte da imagem original: Cloudflare.*

Um navegador acessando o YouTube, por exemplo, atua como cliente. O sistema do YouTube atua como servidor. Quando você abre um vídeo, o cliente envia uma requisição, o servidor processa e devolve os dados necessários para que o vídeo seja exibido.

---

## 1.4 — Exemplo simples

```http
GET /usuarios/123 HTTP/1.1
Host: api.exemplo.com
Accept: application/json
```

Esse trecho representa uma requisição HTTP. O cliente está pedindo ao servidor o usuário de identificador `123`. O cabeçalho `Accept: application/json` informa que o cliente espera receber os dados em JSON.

---

## 1.5 — O que aconteceu no exemplo?

A primeira linha indica o método, o caminho e a versão do protocolo HTTP. O método `GET` indica leitura. O caminho `/usuarios/123` identifica o recurso desejado. O `Host` informa o domínio da API. O `Accept` informa o formato de resposta esperado.

Na prática, uma API funciona como uma porta de entrada controlada. O cliente pede algo de maneira padronizada e o servidor decide como processar internamente essa solicitação.

---

## 1.6 — Quando usar APIs?

APIs são usadas quando um sistema precisa se comunicar com outro. Alguns exemplos comuns são:

- um frontend consumindo dados de um backend;
- um aplicativo mobile fazendo login em um servidor;
- um sistema de pagamento notificando uma loja virtual;
- um backend consultando outro microsserviço;
- uma integração com serviços externos, como mapas, e-mail, autenticação ou gateway de pagamento.

---

## 1.7 — O que pode dar errado?

Um erro comum é pensar que API é apenas uma URL. A URL é só uma parte da API. Uma API envolve método HTTP, caminho, parâmetros, headers, corpo da requisição, autenticação, formato da resposta, códigos de status e regras de negócio.

Outro erro comum é achar que o cliente precisa saber como o servidor foi implementado. Em uma boa API, o cliente não precisa saber se o backend usa Python, Java, Node.js, PostgreSQL, MongoDB ou qualquer outra tecnologia. Ele só precisa saber como se comunicar com a interface pública.

---

## 1.8 — Resumo do capítulo

API é uma interface de comunicação entre sistemas. Ela define um contrato para que clientes possam solicitar dados ou executar ações em um servidor. No modelo cliente-servidor, o cliente faz a requisição e o servidor devolve uma resposta.

---

## 1.9 — Exercícios

1. Explique com suas palavras o que é uma API.
2. Dê três exemplos de sistemas que usam APIs no dia a dia.
3. Identifique quem é o cliente e quem é o servidor em um aplicativo de banco.
4. Explique por que o cliente não deve depender dos detalhes internos do servidor.

---

## 1.10 — Desafios

1. Escolha um aplicativo que você usa diariamente e imagine três APIs que ele provavelmente consome.
2. Desenhe um fluxo simples mostrando cliente, servidor, requisição e resposta.

---

## 1.11 — Fixando o conhecimento

- API é uma interface entre sistemas.
- Cliente faz requisições.
- Servidor processa e responde.
- O contrato da API deve ser claro, previsível e estável.

---

# Capítulo 2 — HTTP, requisições e respostas

A maioria das APIs web usa HTTP como base de comunicação. Por isso, antes de falar sobre REST, é importante entender o que existe dentro de uma requisição e de uma resposta HTTP.

Ao final deste capítulo, você será capaz de:

- entender a estrutura básica de uma requisição HTTP;
- diferenciar método, URL, header e body;
- interpretar códigos de status;
- reconhecer quando usar os principais métodos HTTP.

---

## 2.1 — O problema

Quando o cliente conversa com uma API, ele precisa informar o que deseja fazer. Ler dados é diferente de criar dados. Criar um usuário é diferente de apagar um usuário. O HTTP organiza essa comunicação usando métodos, headers, corpo da mensagem e códigos de status.

---

## 2.2 — O que é HTTP?

HTTP significa **Hypertext Transfer Protocol**. Ele é um protocolo de aplicação usado para transferência de mensagens entre clientes e servidores. Em APIs web, o HTTP é usado para enviar requisições e receber respostas.

Uma requisição HTTP geralmente possui:

- **método:** ação desejada, como `GET`, `POST`, `PUT`, `PATCH` ou `DELETE`;
- **URL ou caminho:** recurso acessado;
- **headers:** metadados da requisição;
- **body:** dados enviados ao servidor, quando necessário.

Uma resposta HTTP geralmente possui:

- **status code:** código indicando o resultado;
- **headers:** metadados da resposta;
- **body:** dados retornados pelo servidor.

---

## 2.3 — Métodos HTTP principais

| Método | Uso comum | Exemplo |
|---|---|---|
| `GET` | Buscar dados | `GET /usuarios` |
| `POST` | Criar um novo recurso ou executar uma ação | `POST /usuarios` |
| `PUT` | Substituir um recurso inteiro | `PUT /usuarios/123` |
| `PATCH` | Atualizar parte de um recurso | `PATCH /usuarios/123` |
| `DELETE` | Remover um recurso | `DELETE /usuarios/123` |
| `OPTIONS` | Consultar opções de comunicação | usado em preflight CORS |

O método deve combinar com a intenção da operação. Se uma operação apenas busca dados, normalmente usamos `GET`. Se ela cria um novo recurso, normalmente usamos `POST`.

---

## 2.4 — Códigos de status

Os códigos de status informam o resultado da requisição. Eles são agrupados por famílias:

| Família | Significado | Exemplos |
|---|---|---|
| `2xx` | Sucesso | `200 OK`, `201 Created`, `204 No Content` |
| `3xx` | Redirecionamento | `301 Moved Permanently`, `304 Not Modified` |
| `4xx` | Erro do cliente | `400 Bad Request`, `401 Unauthorized`, `403 Forbidden`, `404 Not Found` |
| `5xx` | Erro do servidor | `500 Internal Server Error`, `503 Service Unavailable` |

Um erro `404`, por exemplo, não significa necessariamente que a API inteira está fora do ar. Ele normalmente significa que o recurso solicitado não foi encontrado.

---

## 2.5 — Exemplo de resposta HTTP

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
  "id": 123,
  "nome": "Maria Oliveira",
  "email": "maria@example.com"
}
```

---

## 2.6 — O que aconteceu no exemplo?

A primeira linha mostra que a requisição foi processada com sucesso usando o status `200 OK`. O header `Content-Type: application/json` informa que o corpo da resposta está em JSON. O body contém os dados do usuário solicitado.

---

## 2.7 — O que pode dar errado?

Um problema comum é devolver sempre `200 OK`, mesmo quando ocorreu erro. Isso dificulta a vida do cliente, porque ele precisa interpretar o corpo da resposta para descobrir se deu certo ou não. O ideal é usar status codes compatíveis com o resultado da operação.

Outro problema comum é confundir `401` e `403`. O status `401 Unauthorized` costuma indicar ausência ou falha de autenticação. O status `403 Forbidden` indica que o servidor entendeu quem é o usuário, mas ele não tem permissão para acessar aquele recurso.

---

## 2.8 — Um pouco mais

Em APIs profissionais, status codes devem ser usados junto com mensagens de erro claras. Uma resposta de erro útil não deve expor detalhes internos do servidor, mas deve ajudar o cliente a entender o que precisa corrigir.

Exemplo:

```json
{
  "error": "validation_error",
  "message": "O campo email é obrigatório.",
  "field": "email"
}
```

Esse retorno é mais útil do que uma mensagem genérica como `Erro interno` quando o problema foi causado por dados inválidos enviados pelo cliente.

---

## 2.9 — Resumo do capítulo

HTTP é a base mais comum para APIs web. Uma requisição possui método, caminho, headers e, em alguns casos, body. Uma resposta possui status code, headers e body. O uso correto de métodos e status codes deixa a API mais previsível.

---

## 2.10 — Exercícios

1. Qual método HTTP você usaria para listar produtos?
2. Qual método HTTP você usaria para criar um usuário?
3. Qual status code você retornaria quando um recurso não existe?
4. Qual status code você retornaria após criar um recurso com sucesso?

---

## 2.11 — Desafios

1. Monte uma tabela com cinco endpoints de uma API de biblioteca.
2. Para cada endpoint, defina método, caminho, objetivo e status esperado.

---

## 2.12 — Fixando o conhecimento

- `GET` busca dados.
- `POST` cria recursos ou executa ações.
- `PUT` substitui um recurso.
- `PATCH` altera parte de um recurso.
- `DELETE` remove um recurso.
- Status codes ajudam o cliente a interpretar o resultado.

---

# Capítulo 3 — REST e APIs RESTful

REST é um dos estilos arquiteturais mais usados para construir APIs web. Porém, REST não é uma biblioteca, framework ou protocolo. REST é um conjunto de restrições arquiteturais para projetar sistemas distribuídos.

Ao final deste capítulo, você será capaz de:

- explicar o que é REST;
- entender as principais restrições REST;
- diferenciar REST de RESTful;
- entender idempotência;
- projetar endpoints mais consistentes.

---

## 3.1 — O problema

Sem um padrão, cada API poderia inventar sua própria forma de representar ações. Uma API poderia usar `/buscarUsuario`, outra `/get-user`, outra `/consultaCliente`, e assim por diante. Isso torna a comunicação confusa, difícil de documentar e difícil de manter.

REST ajuda a organizar APIs ao tratar as informações como recursos identificados por URLs e manipulados por métodos HTTP.

---

## 3.2 — O que é REST?

REST significa **Representational State Transfer**. Em uma API REST, o cliente interage com recursos. Um recurso pode ser um usuário, pedido, produto, pagamento, endereço ou qualquer informação importante para o sistema.

Em vez de pensar primeiro em ações, uma API REST pensa primeiro em recursos:

```text
/usuarios
/usuarios/123
/pedidos
/pedidos/987
```

Depois, os métodos HTTP indicam o que será feito com esses recursos:

```http
GET /usuarios/123
DELETE /usuarios/123
PATCH /usuarios/123
```

---

## 3.3 — RESTful

Uma API é chamada de **RESTful** quando segue bem os princípios e restrições do estilo REST. Na prática do mercado, muitas APIs chamadas de RESTful seguem apenas parte dessas ideias, principalmente uso de recursos, métodos HTTP, status codes e respostas em JSON.

O ponto mais importante para o iniciante é: uma API RESTful deve ser previsível. Ao olhar para um endpoint, o desenvolvedor deve conseguir entender qual recurso está sendo acessado e qual ação será executada.

---

## 3.4 — As seis restrições REST

| Restrição | Explicação didática |
|---|---|
| Cliente-servidor | Cliente e servidor têm responsabilidades separadas. O cliente cuida da interface ou consumo; o servidor cuida dos dados e regras. |
| Stateless | Cada requisição deve carregar as informações necessárias. O servidor não deve depender de uma conversa anterior para entender a requisição atual. |
| Cacheável | Respostas devem indicar se podem ou não ser armazenadas em cache. Isso melhora desempenho e reduz chamadas desnecessárias. |
| Interface uniforme | A API deve ter uma forma consistente de acessar e manipular recursos. |
| Sistema em camadas | Podem existir intermediários, como gateways, proxies, caches e balanceadores, sem que o cliente precise conhecer todos eles. |
| Código sob demanda | Opcional. O servidor pode enviar código executável ao cliente, como scripts, mas isso não é comum em APIs JSON tradicionais. |

---

## 3.5 — Exemplo de modelagem REST

Imagine uma API de usuários.

```http
GET /usuarios
GET /usuarios/123
POST /usuarios
PATCH /usuarios/123
DELETE /usuarios/123
```

Esses endpoints são mais consistentes do que:

```http
GET /buscarUsuarios
GET /pegarUsuario?id=123
POST /criarNovoUsuario
POST /alterarUsuario
GET /deletarUsuario/123
```

---

## 3.6 — O que aconteceu no exemplo?

No primeiro grupo, os caminhos representam recursos: `/usuarios` e `/usuarios/123`. Os métodos HTTP representam ações: buscar, criar, atualizar ou remover.

No segundo grupo, os caminhos misturam verbos e ações. Isso deixa a API menos padronizada. O problema não é apenas estética. APIs inconsistentes são mais difíceis de aprender, documentar, testar e manter.

---

## 3.7 — Idempotência

Idempotência significa que repetir a mesma operação várias vezes produz o mesmo estado final no servidor.

Exemplo com `DELETE`:

```http
DELETE /usuarios/123
DELETE /usuarios/123
DELETE /usuarios/123
```

Se o usuário foi removido na primeira chamada, as chamadas seguintes não removem “mais ainda” o usuário. O estado final continua sendo: usuário inexistente.

Exemplo com `PUT`:

```http
PUT /usuarios/123
Content-Type: application/json

{
  "nome": "Ana",
  "email": "ana@example.com"
}
```

Se essa mesma requisição for enviada várias vezes, o estado final do recurso será o mesmo: o usuário `123` terá exatamente esses dados.

---

## 3.8 — Quando usar PUT ou PATCH?

Use `PUT` quando a intenção for substituir a representação inteira de um recurso. Use `PATCH` quando a intenção for atualizar apenas alguns campos.

Exemplo de `PATCH`:

```http
PATCH /usuarios/123
Content-Type: application/json

{
  "email": "novo-email@example.com"
}
```

Nesse caso, apenas o e-mail será atualizado. Os demais campos permanecem como estão.

---

## 3.9 — O que pode dar errado?

Um erro comum é usar `GET` para alterar dados, como em `/aprovarPedido?id=10`. Requisições `GET` devem ser seguras no sentido de não modificar o estado do recurso. Se uma ação altera dados, ela normalmente deve usar `POST`, `PUT`, `PATCH` ou `DELETE`.

Outro erro comum é criar endpoints com nomes de ações em excesso. Nem toda ação precisa virar verbo na URL. Muitas vezes, o recurso pode ser modelado melhor.

Exemplo:

```http
POST /pedidos/987/cancelamentos
```

Esse endpoint representa a criação de um cancelamento para o pedido `987`, em vez de usar algo como:

```http
POST /cancelarPedido/987
```

---

## 3.10 — Um pouco mais

REST também está relacionado ao conceito de representações. O cliente não acessa diretamente o objeto interno do servidor. Ele recebe uma representação daquele recurso, geralmente em JSON.

Um mesmo recurso poderia ter diferentes representações, como JSON, XML ou HTML. Em APIs modernas, JSON é o formato mais comum por ser simples, leve e fácil de manipular.

---

## 3.11 — Resumo do capítulo

REST organiza APIs em torno de recursos. Os métodos HTTP indicam o que será feito com esses recursos. Uma API RESTful bem feita é previsível, stateless, consistente e usa status codes de forma adequada.

---

## 3.12 — Exercícios

1. Transforme `/buscarProdutos` em um endpoint mais RESTful.
2. Transforme `/deletarProduto/10` em um endpoint mais RESTful.
3. Explique a diferença entre `PUT` e `PATCH`.
4. Explique por que `GET /aprovarPagamento/123` não é uma boa ideia.

---

## 3.13 — Desafios

1. Modele cinco endpoints REST para uma API de tarefas.
2. Modele três endpoints REST para uma API de pagamentos.
3. Crie um exemplo de operação idempotente e outro de operação não idempotente.

---

## 3.14 — Fixando o conhecimento

- REST é um estilo arquitetural.
- RESTful é uma API que segue bem esse estilo.
- Recursos devem ser representados por substantivos na URL.
- Métodos HTTP indicam a ação.
- Idempotência ajuda a tornar operações previsíveis e seguras para repetição.

---

# Capítulo 4 — Parâmetros, headers, body e cookies

Uma requisição HTTP pode carregar informações em lugares diferentes. Entender isso ajuda a projetar e consumir APIs com mais clareza.

Ao final deste capítulo, você será capaz de:

- diferenciar path parameters e query parameters;
- entender quando usar headers;
- entender quando usar body;
- saber onde cookies entram em uma requisição.

---

## 4.1 — O problema

Quando o cliente chama uma API, ele muitas vezes precisa enviar dados adicionais. Alguns dados identificam o recurso, outros filtram uma busca, outros autenticam a requisição e outros representam o conteúdo que será criado ou atualizado.

Colocar tudo no mesmo lugar deixa a API confusa. Por isso, usamos partes diferentes da requisição para finalidades diferentes.

---

## 4.2 — Path parameters

Path parameters são parâmetros que fazem parte do caminho da URL. Eles normalmente identificam um recurso específico.

```http
GET /usuarios/123
```

Nesse exemplo, `123` é um path parameter. Ele indica qual usuário o cliente deseja buscar.

Outro exemplo:

```http
GET /usuarios/123/pedidos/987
```

Aqui, a URL indica o pedido `987` relacionado ao usuário `123`.

---

## 4.3 — Query parameters

Query parameters aparecem depois do símbolo `?` e são separados por `&`. Eles são muito usados para filtros, busca, ordenação e paginação.

```http
GET /usuarios?status=ativo&idade=30
```

Nesse exemplo, o cliente está buscando usuários com status ativo e idade igual a 30.

Outros exemplos:

```http
GET /produtos?categoria=livros
GET /produtos?sort=preco&page=2&limit=20
GET /pedidos?status=pago&dataInicio=2026-01-01
```

---

## 4.4 — Diferença prática entre path e query

Use **path parameter** quando o dado for essencial para identificar o recurso.

```http
GET /usuarios/123
```

Use **query parameter** quando o dado for usado para filtrar, ordenar, paginar ou customizar uma listagem.

```http
GET /usuarios?status=ativo
```

Uma forma simples de pensar é: se sem aquele valor a URL não sabe qual recurso acessar, provavelmente é path. Se sem aquele valor a API ainda consegue retornar uma lista padrão, provavelmente é query.

---

## 4.5 — Headers

Headers carregam metadados da requisição. Eles não representam diretamente o recurso, mas informam detalhes sobre como a comunicação deve acontecer.

Exemplos comuns:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
Content-Type: application/json
Accept: application/json
User-Agent: MeuCliente/1.0
```

O header `Authorization` pode enviar credenciais. O `Content-Type` informa o formato do body enviado. O `Accept` informa o formato de resposta esperado.

---

## 4.6 — Body

O body é o corpo da requisição. Ele é usado quando o cliente precisa enviar dados mais completos para o servidor, como na criação ou atualização de recursos.

```http
POST /usuarios
Content-Type: application/json

{
  "nome": "João Silva",
  "email": "joao.silva@example.com",
  "idade": 30
}
```

---

## 4.7 — Cookies

Cookies são pequenos dados enviados pelo navegador junto com requisições para uma origem específica. Eles são muito usados em autenticação baseada em sessão, preferências de usuário e rastreamento de estado em aplicações web.

Em APIs modernas, é comum encontrar autenticação via header `Authorization`, mas cookies ainda são muito usados em aplicações web tradicionais e em autenticação com sessão.

---

## 4.8 — O que pode dar errado?

Um erro comum é enviar dados sensíveis em query parameters, como tokens ou senhas:

```http
GET /usuarios?token=abc123
```

Isso é ruim porque URLs podem aparecer em histórico, logs, ferramentas de monitoramento e caches. Dados sensíveis devem ser enviados com cuidado, geralmente em headers seguros e sempre usando HTTPS.

Outro erro comum é usar body em requisições `GET`. Embora tecnicamente existam discussões sobre isso, na prática é melhor evitar, pois muitos clientes, servidores e intermediários não tratam body em `GET` de maneira consistente.

---

## 4.9 — Resumo do capítulo

Path parameters identificam recursos. Query parameters filtram ou modificam consultas. Headers carregam metadados. Body carrega dados mais completos. Cookies podem transportar estado entre navegador e servidor.

---

## 4.10 — Exercícios

1. Em `/usuarios/55`, qual parte é o path parameter?
2. Em `/produtos?categoria=teclado&page=2`, quais são os query parameters?
3. Onde você enviaria um token de autenticação?
4. Onde você enviaria os dados para criar um usuário?

---

## 4.11 — Desafios

1. Crie três endpoints de listagem usando query parameters.
2. Crie três endpoints de detalhe usando path parameters.
3. Escreva uma requisição `POST /produtos` com headers e body JSON.

---

## 4.12 — Fixando o conhecimento

- Path parameter identifica recurso.
- Query parameter filtra, ordena ou pagina.
- Header envia metadados.
- Body envia dados da entidade.
- Cookies podem carregar estado em aplicações web.

---

# Capítulo 5 — JSON em APIs

JSON é um dos formatos mais usados para troca de dados em APIs web. Ele é simples, legível e fácil de converter para estruturas de dados em várias linguagens.

Ao final deste capítulo, você será capaz de:

- entender a estrutura básica do JSON;
- escrever objetos e listas em JSON;
- diferenciar JSON de JSON:API;
- interpretar payloads de requisição e resposta.

---

## 5.1 — O problema

Cliente e servidor podem ser feitos em tecnologias diferentes. O frontend pode estar em JavaScript, o backend em Python e outro serviço em Java. Para que todos consigam trocar dados, é necessário usar um formato comum.

JSON é uma solução popular para esse problema.

---

## 5.2 — O que é JSON?

JSON significa **JavaScript Object Notation**. Apesar do nome, ele não é exclusivo do JavaScript. JSON é um formato textual para representar dados estruturados.

Um JSON pode representar objetos, listas, textos, números, booleanos e valores nulos.

```json
{
  "nome": "Diego",
  "idade": 27,
  "cidades": ["São Paulo", "Rio de Janeiro"],
  "ativo": true
}
```

---

## 5.3 — O que aconteceu no exemplo?

O JSON acima representa um objeto. Cada informação aparece como um par chave-valor. A chave sempre fica entre aspas duplas. O valor pode ser texto, número, lista, booleano, objeto ou `null`.

A chave `cidades` possui uma lista de strings. A chave `ativo` possui um valor booleano. Essa estrutura é simples de ler e muito comum em respostas de APIs.

---

## 5.4 — Exemplo de criação de usuário

```http
POST /api/usuarios
Content-Type: application/json

{
  "nome": "João Silva",
  "email": "joao.silva@example.com",
  "idade": 30,
  "cidade": "São Paulo"
}
```

---

## 5.5 — O que aconteceu no exemplo?

O cliente está enviando uma requisição `POST` para criar um usuário. O header `Content-Type: application/json` informa que o corpo da requisição está em JSON. O body contém os dados necessários para criar o recurso.

O servidor deve validar esses dados antes de salvar. Por exemplo, ele pode verificar se o e-mail é válido, se a idade é numérica e se os campos obrigatórios foram enviados.

---

## 5.6 — Resposta possível

```http
HTTP/1.1 201 Created
Content-Type: application/json

{
  "id": 321,
  "nome": "João Silva",
  "email": "joao.silva@example.com",
  "idade": 30,
  "cidade": "São Paulo"
}
```

O status `201 Created` indica que o recurso foi criado. O body devolve a representação do usuário criado, agora com o identificador gerado pelo servidor.

---

## 5.7 — JSON e JSON:API não são a mesma coisa

É comum usar a expressão “API JSON” para falar de uma API que envia e recebe JSON. Isso é diferente de **JSON:API**, que é uma especificação com regras próprias para estruturar documentos JSON, relacionamentos, erros, paginação e outros detalhes.

Nesta apostila, quando falamos em JSON em APIs, estamos falando do uso geral de JSON como formato de troca de dados. Quando a especificação formal for necessária, use o nome **JSON:API**.

---

## 5.8 — O que pode dar errado?

Um erro comum é esquecer aspas duplas nas chaves:

```json
{
  nome: "João"
}
```

Esse conteúdo parece objeto JavaScript, mas não é JSON válido. O correto é:

```json
{
  "nome": "João"
}
```

Outro erro comum é deixar vírgula sobrando no último campo:

```json
{
  "nome": "João",
  "email": "joao@example.com",
}
```

O correto é remover a última vírgula:

```json
{
  "nome": "João",
  "email": "joao@example.com"
}
```

---

## 5.9 — Resumo do capítulo

JSON é um formato textual usado para representar dados estruturados. Em APIs, ele aparece com frequência no corpo de requisições e respostas. JSON deve seguir regras de sintaxe, como chaves entre aspas duplas e ausência de vírgula final.

---

## 5.10 — Exercícios

1. Escreva um JSON representando um produto.
2. Escreva um JSON representando uma lista de três tarefas.
3. Corrija um JSON inválido com chave sem aspas.
4. Explique a função do header `Content-Type: application/json`.

---

## 5.11 — Desafios

1. Crie o payload JSON para cadastrar um livro.
2. Crie uma resposta JSON para listar três livros.
3. Crie uma resposta de erro em JSON para campo obrigatório ausente.

---

## 5.12 — Fixando o conhecimento

- JSON é texto estruturado.
- Chaves devem usar aspas duplas.
- APIs usam JSON em requisições e respostas.
- JSON:API é uma especificação específica, não apenas “qualquer API com JSON”.

---

# Capítulo 6 — Estilos e protocolos de API

Nem toda API usa REST. Existem diferentes estilos e protocolos, cada um com vantagens e desvantagens. Conhecer essas opções ajuda a escolher a abordagem correta para cada problema.

Ao final deste capítulo, você será capaz de:

- comparar REST, SOAP, GraphQL, gRPC e WebSocket;
- entender quando cada abordagem costuma ser usada;
- reconhecer diferenças entre APIs síncronas, flexíveis, contratuais e em tempo real.

---

## 6.1 — O problema

Uma API de consulta de produtos não tem as mesmas necessidades de um chat em tempo real. Uma integração bancária corporativa pode exigir contratos formais. Uma comunicação interna entre microsserviços pode exigir alta performance. Por isso, existem estilos e protocolos diferentes.

---

## 6.2 — REST

REST é muito usado em APIs web por ser simples, amplo e baseado em conceitos conhecidos do HTTP. Ele combina bem com aplicações CRUD, integrações públicas, sistemas web e mobile.

Exemplo:

```http
GET /produtos/10
Accept: application/json
```

Esse estilo é uma boa escolha quando você precisa expor recursos de maneira simples e previsível.

---

## 6.3 — SOAP

SOAP significa **Simple Object Access Protocol**. É um protocolo baseado em XML e mais comum em ambientes corporativos, sistemas legados e integrações que exigem contratos formais.

Uma mensagem SOAP tende a ser mais verbosa que uma chamada REST simples.

```xml
<soap:Envelope>
  <soap:Body>
    <ConsultarCliente>
      <clienteId>123</clienteId>
    </ConsultarCliente>
  </soap:Body>
</soap:Envelope>
```

SOAP não é “errado” por ser antigo. Ele apenas resolve problemas diferentes e costuma trazer mais formalidade e complexidade.

---

## 6.4 — GraphQL

GraphQL é uma linguagem de consulta para APIs. Diferente de REST, onde o servidor define vários endpoints, em GraphQL o cliente descreve os campos que deseja receber.

Exemplo:

```graphql
query {
  usuario(id: "123") {
    nome
    email
  }
}
```

Essa consulta pede apenas `nome` e `email` do usuário. Isso ajuda a evitar dois problemas comuns:

- **overfetching:** receber dados demais;
- **underfetching:** receber dados de menos e precisar fazer várias chamadas.

GraphQL é útil quando clientes diferentes precisam de formatos diferentes de dados, como web, mobile e painéis administrativos.

---

## 6.5 — gRPC

gRPC é um framework de RPC moderno. Em vez de pensar em recursos e métodos HTTP visíveis ao desenvolvedor, ele trabalha com chamadas de procedimentos definidos em contratos. É muito usado em comunicação interna entre serviços, especialmente quando performance e tipagem forte são importantes.

Em gRPC, os contratos normalmente são definidos em arquivos `.proto`.

```proto
service UsuarioService {
  rpc BuscarUsuario (BuscarUsuarioRequest) returns (UsuarioResponse);
}
```

Esse contrato indica que existe uma operação chamada `BuscarUsuario`, que recebe uma requisição e devolve uma resposta.

---

## 6.6 — WebSocket

WebSocket permite comunicação bidirecional persistente entre cliente e servidor. Diferente do modelo HTTP tradicional, em que o cliente faz uma requisição e o servidor responde, no WebSocket a conexão permanece aberta para troca contínua de mensagens.

Exemplo de uso:

- chat em tempo real;
- jogos online;
- painel de monitoramento;
- notificações instantâneas;
- cotações e eventos em tempo real.

WebSocket é útil quando o servidor precisa enviar dados ao cliente assim que algo acontece, sem depender de várias requisições repetidas.

---

## 6.7 — Comparação prática

| Abordagem | Melhor uso | Pontos fortes | Atenção |
|---|---|---|---|
| REST | APIs web e CRUD | Simples, conhecido, fácil de testar | Pode gerar muitas chamadas em telas complexas |
| SOAP | Integrações corporativas formais | Contrato forte, padronização XML | Mais verboso e complexo |
| GraphQL | Clientes com necessidades diferentes de dados | Flexibilidade de consulta | Exige cuidado com performance e autorização por campo |
| gRPC | Comunicação interna entre serviços | Alta performance, contratos fortes | Menos amigável para consumo direto em navegador |
| WebSocket | Tempo real | Comunicação bidirecional contínua | Exige controle de conexão, reconexão e escala |

---

## 6.8 — O que pode dar errado?

Um erro comum é escolher uma tecnologia por moda, não por necessidade. REST continua sendo uma excelente escolha para muitas APIs. GraphQL pode ser poderoso, mas adiciona complexidade. gRPC pode ser muito eficiente, mas nem sempre é a melhor opção para APIs públicas simples. WebSocket é ótimo para tempo real, mas desnecessário para dados que mudam pouco.

A escolha deve considerar o problema, o time, os clientes da API, os requisitos de performance, a necessidade de contrato e a facilidade de manutenção.

---

## 6.9 — Resumo do capítulo

REST, SOAP, GraphQL, gRPC e WebSocket resolvem problemas diferentes. APIs profissionais não dependem apenas de escolher a tecnologia mais moderna, mas de escolher a solução mais adequada ao contexto.

---

## 6.10 — Exercícios

1. Qual abordagem você escolheria para uma API CRUD simples?
2. Qual abordagem combina com chat em tempo real?
3. Qual abordagem pode ser útil entre microsserviços com alta performance?
4. Explique o problema de overfetching em GraphQL.

---

## 6.11 — Desafios

1. Compare REST e GraphQL usando um exemplo de tela de perfil de usuário.
2. Pesquise uma API pública REST e identifique seus recursos.
3. Imagine uma arquitetura com frontend, backend e microsserviços. Onde REST e gRPC poderiam aparecer?

---

## 6.12 — Fixando o conhecimento

- REST é simples e comum em APIs web.
- SOAP é formal e baseado em XML.
- GraphQL permite o cliente escolher os campos.
- gRPC é forte para comunicação interna performática.
- WebSocket é indicado para comunicação em tempo real.

---

# Capítulo 7 — Testando APIs com cURL

cURL é uma ferramenta de linha de comando muito usada para testar APIs. Com ela, é possível fazer requisições, enviar headers, mandar JSON, autenticar chamadas e verificar respostas.

Ao final deste capítulo, você será capaz de:

- executar requisições `GET`, `POST`, `PUT`, `PATCH` e `DELETE`;
- enviar headers e body JSON;
- testar autenticação com Bearer Token;
- interpretar comandos básicos de cURL.

---

## 7.1 — O problema

Antes de integrar uma API ao código de uma aplicação, é comum testá-la isoladamente. Ferramentas como Postman, Insomnia e Bruno ajudam bastante, mas o cURL é leve, direto e funciona muito bem no terminal.

---

## 7.2 — GET

```bash
curl https://api.github.com/users/octocat/repos
```

Esse comando faz uma requisição `GET`. Quando o método não é informado, o cURL normalmente usa `GET` por padrão.

Também é possível ser explícito:

```bash
curl -X GET https://api.example.com/usuarios
```

---

## 7.3 — POST com formulário simples

```bash
curl -X POST \
  -d "userId=5&title=PostTitle&body=PostContent" \
  https://jsonplaceholder.typicode.com/posts
```

Esse comando envia dados no corpo da requisição usando `-d`. Quando usamos `-d`, o cURL envia uma requisição com corpo.

---

## 7.4 — POST com JSON

```bash
curl -X POST https://api.example.com/usuarios \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Maria",
    "email": "maria@example.com"
  }'
```

---

## 7.5 — O que aconteceu no exemplo?

O `-X POST` define o método. O `-H` adiciona headers. O header `Content-Type: application/json` informa que o body está em JSON. O `-d` envia o corpo da requisição.

Esse tipo de comando é muito usado para testar endpoints de criação de recursos.

---

## 7.6 — POST com autenticação Bearer

```bash
curl -X POST https://api.example.com/usuarios \
  -H "Accept: application/json" \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer SEU_TOKEN_AQUI" \
  -d '{
    "username": "myusername",
    "email": "myusername@gmail.com",
    "password": "PasswOrd123!"
  }'
```

Nesse exemplo, o header `Authorization` envia um token Bearer. Esse padrão é comum em APIs que usam OAuth 2.0 ou JWT.

---

## 7.7 — PUT

```bash
curl -X PUT https://jsonplaceholder.typicode.com/posts/5 \
  -H "Content-Type: application/json" \
  -d '{
    "userId": 5,
    "title": "Novo título",
    "body": "Novo conteúdo do post."
  }'
```

Use `PUT` quando a intenção for substituir o recurso inteiro.

---

## 7.8 — PATCH

```bash
curl -X PATCH https://jsonplaceholder.typicode.com/posts/5 \
  -H "Content-Type: application/json" \
  -d '{
    "title": "Título atualizado"
  }'
```

Use `PATCH` quando a intenção for atualizar apenas parte do recurso.

---

## 7.9 — DELETE

```bash
curl -X DELETE https://jsonplaceholder.typicode.com/posts/5
```

Esse comando solicita a remoção do recurso identificado por `/posts/5`.

---

## 7.10 — Visualizando headers da resposta

```bash
curl -i https://api.example.com/usuarios
```

A opção `-i` mostra os headers da resposta junto com o body. Isso é útil para verificar status code, `Content-Type`, cache e outros metadados.

---

## 7.11 — O que pode dar errado?

Um erro comum é esquecer o header `Content-Type` ao enviar JSON. Sem ele, o servidor pode não interpretar corretamente o body.

Outro problema é copiar tokens reais para exemplos públicos. Nunca publique tokens, senhas ou chaves de API em repositórios. Em materiais didáticos, use valores falsos como `SEU_TOKEN_AQUI`.

---

## 7.12 — Resumo do capítulo

cURL permite testar APIs pelo terminal. Com ele, você consegue definir método, headers, body e autenticação. É uma ferramenta essencial para desenvolvedores backend porque ajuda a isolar problemas entre cliente, servidor e rede.

---

## 7.13 — Exercícios

1. Escreva um comando cURL para listar usuários.
2. Escreva um comando cURL para criar um produto com JSON.
3. Adicione um header `Authorization` em um comando cURL.
4. Use `curl -i` e observe os headers de uma resposta.

---

## 7.14 — Desafios

1. Teste uma API pública com cURL.
2. Faça uma requisição `POST` para o JSONPlaceholder.
3. Simule um erro alterando a URL e observe o status retornado.

---

## 7.15 — Fixando o conhecimento

- cURL testa APIs no terminal.
- `-X` define método.
- `-H` adiciona headers.
- `-d` envia body.
- `-i` exibe headers da resposta.

---

# Capítulo 8 — CORS

CORS é um tema que confunde muitos desenvolvedores porque o erro aparece no navegador, mas a correção geralmente fica no backend.

Ao final deste capítulo, você será capaz de:

- explicar o que é origem;
- entender o papel do navegador no CORS;
- diferenciar requisições simples e preflight;
- identificar por que Postman e cURL não reproduzem erros de CORS.

---

## 8.1 — O problema

Imagine um frontend rodando em:

```text
http://localhost:3000
```

E uma API rodando em:

```text
http://localhost:8000
```

Mesmo estando na mesma máquina, essas duas URLs têm portas diferentes. Para o navegador, isso representa origens diferentes. Por segurança, o navegador não permite livremente que uma página de uma origem acesse recursos de outra origem sem autorização do servidor.

---

## 8.2 — O que é origem?

Origem é a combinação de:

- protocolo;
- domínio;
- porta.

Exemplo:

```text
https://meusite.com:443
```

Se qualquer uma dessas partes mudar, a origem muda.

| URL | Mesma origem de `https://api.exemplo.com`? | Motivo |
|---|---|---|
| `https://api.exemplo.com` | Sim | Mesmo protocolo, domínio e porta padrão |
| `http://api.exemplo.com` | Não | Protocolo diferente |
| `https://www.exemplo.com` | Não | Domínio diferente |
| `https://api.exemplo.com:8443` | Não | Porta diferente |

---

## 8.3 — O que é CORS?

CORS significa **Cross-Origin Resource Sharing**. É um mecanismo usado pelos navegadores para controlar requisições entre origens diferentes.

Importante: CORS não é uma configuração do frontend para “liberar tudo”. Quem libera ou bloqueia o acesso é o servidor, por meio de headers HTTP. O navegador apenas aplica essa política de segurança.

---

## 8.4 — Headers CORS comuns

| Header | Função |
|---|---|
| `Access-Control-Allow-Origin` | Informa quais origens podem acessar o recurso. |
| `Access-Control-Allow-Methods` | Informa quais métodos são permitidos. |
| `Access-Control-Allow-Headers` | Informa quais headers podem ser enviados pelo cliente. |
| `Access-Control-Allow-Credentials` | Indica se credenciais como cookies podem ser incluídas. |

Exemplo:

```http
Access-Control-Allow-Origin: https://meufrontend.com
Access-Control-Allow-Methods: GET, POST, PATCH, DELETE
Access-Control-Allow-Headers: Content-Type, Authorization
```

---

## 8.5 — Requisições simples e preflight

Algumas requisições são consideradas simples. Outras exigem uma requisição prévia chamada **preflight**.

No preflight, o navegador envia uma requisição `OPTIONS` antes da requisição real para perguntar ao servidor se aquela chamada é permitida.

Exemplo:

```http
OPTIONS /usuarios HTTP/1.1
Origin: https://meufrontend.com
Access-Control-Request-Method: POST
Access-Control-Request-Headers: Content-Type, Authorization
```

Se o servidor responder com os headers CORS adequados, o navegador envia a requisição real.

---

## 8.6 — O que aconteceu no exemplo?

O navegador informou a origem do frontend, o método desejado e os headers que pretende enviar. O servidor deve responder dizendo se permite ou não essa combinação.

Esse processo protege o usuário contra páginas maliciosas tentando acessar recursos de outra origem sem autorização adequada.

---

## 8.7 — Por que funciona no cURL e falha no navegador?

CORS é uma política aplicada pelos navegadores. Ferramentas como cURL, Postman e Insomnia não bloqueiam requisições por CORS da mesma forma. Por isso, uma API pode funcionar no Postman e falhar no frontend com erro de CORS.

Quando isso acontece, o problema geralmente está na configuração de CORS do servidor ou na combinação de origem, método, headers e credenciais.

---

## 8.8 — O que pode dar errado?

Um erro comum é usar `Access-Control-Allow-Origin: *` junto com credenciais. Quando cookies ou credenciais estão envolvidos, o servidor deve liberar uma origem específica, não um curinga genérico.

Outro erro comum é liberar o método `POST`, mas esquecer de liberar o header `Authorization`. Nesse caso, a requisição pode falhar no preflight antes mesmo de chegar ao endpoint real.

---

## 8.9 — Resumo do capítulo

CORS é um mecanismo de segurança aplicado pelos navegadores para requisições entre origens diferentes. O backend precisa responder com headers adequados. cURL e Postman não são bons testes para validar comportamento CORS do navegador.

---

## 8.10 — Exercícios

1. Explique o que define uma origem.
2. Diga se `http://localhost:3000` e `http://localhost:8000` são a mesma origem.
3. Para que serve `Access-Control-Allow-Origin`?
4. Por que uma chamada pode funcionar no Postman e falhar no navegador?

---

## 8.11 — Desafios

1. Pesquise como configurar CORS no framework backend que você usa.
2. Simule uma chamada de frontend para backend em portas diferentes.
3. Observe no DevTools do navegador se houve requisição `OPTIONS`.

---

## 8.12 — Fixando o conhecimento

- Origem é protocolo + domínio + porta.
- CORS é aplicado pelo navegador.
- O servidor libera acesso por headers.
- Preflight usa método `OPTIONS`.
- Postman e cURL não reproduzem exatamente o bloqueio de CORS do navegador.

---

# Capítulo 9 — Autenticação e autorização em APIs

APIs frequentemente protegem recursos. Nem todo usuário pode acessar tudo. Para isso, usamos autenticação e autorização.

Ao final deste capítulo, você será capaz de:

- diferenciar autenticação e autorização;
- entender métodos comuns de autenticação;
- explicar a estrutura básica de um JWT;
- entender o fluxo de login com token;
- reconhecer cuidados de segurança.

---

## 9.1 — O problema

Uma API de banco não pode devolver o saldo de qualquer cliente para qualquer pessoa. Ela precisa primeiro identificar quem está fazendo a requisição e depois verificar se essa pessoa tem permissão para acessar aquele recurso.

---

## 9.2 — Autenticação e autorização

**Autenticação** responde à pergunta: “quem é você?”.

**Autorização** responde à pergunta: “o que você pode acessar?”.

Um usuário pode estar autenticado e ainda assim não ter autorização para acessar um recurso. Por exemplo, um funcionário comum pode fazer login no sistema, mas não ter permissão para acessar relatórios administrativos.

---

## 9.3 — Métodos comuns

| Método | Como funciona | Observação |
|---|---|---|
| API Key | Cliente envia uma chave de acesso. | Simples, mas exige cuidado com vazamento e escopo. |
| Basic Auth | Cliente envia usuário e senha codificados em Base64. | Deve ser usado apenas com HTTPS. |
| Bearer Token | Cliente envia um token no header `Authorization`. | Muito usado com OAuth 2.0 e JWT. |
| OAuth 2.0 | Framework de autorização para acesso delegado. | Muito usado em login social e integrações. |
| OpenID Connect | Camada de identidade sobre OAuth 2.0. | Usado para autenticação federada. |
| JWT | Formato compacto de token com claims e assinatura. | Pode ser usado em autenticação stateless. |

---

## 9.4 — JWT

JWT significa **JSON Web Token**. Ele é um formato compacto para representar informações entre duas partes. Um JWT normalmente possui três partes:

```text
header.payload.signature
```

![Estrutura de um JWT](./images/api/jwt-token.png)

*Figura 2 — Estrutura simplificada de um JWT. Fonte da imagem original: JWT.io.*

As partes são:

| Parte | Função |
|---|---|
| Header | Informa o tipo do token e o algoritmo usado. |
| Payload | Carrega claims, como identificador do usuário, permissões e expiração. |
| Signature | Assinatura usada para verificar se o token não foi alterado. |

---

## 9.5 — Exemplo de envio de token

```http
GET /me HTTP/1.1
Host: api.exemplo.com
Authorization: Bearer SEU_TOKEN_AQUI
Accept: application/json
```

---

## 9.6 — O que aconteceu no exemplo?

O cliente está acessando o endpoint `/me`, que normalmente devolve dados do usuário autenticado. O token é enviado no header `Authorization` usando o esquema `Bearer`.

O servidor deve validar o token antes de responder. Essa validação pode incluir assinatura, expiração, emissor, audiência e permissões.

---

## 9.7 — Fluxo básico com JWT

![Fluxo básico de autenticação com JWT](./images/api/jwt-flow.png)

*Figura 3 — Fluxo básico de autenticação com JWT. Fonte da imagem original: roadmap.sh.*

Fluxo comum:

1. Cliente envia credenciais no login.
2. Servidor valida as credenciais.
3. Servidor gera um token.
4. Cliente armazena o token com cuidado.
5. Cliente envia o token nas próximas requisições.
6. Servidor valida o token antes de liberar o recurso.

---

## 9.8 — Cuidados importantes

Tokens são credenciais. Quem possui um token válido pode tentar usá-lo para acessar a API. Por isso:

- não publique tokens em repositórios;
- use HTTPS;
- defina tempo de expiração;
- valide assinatura e claims relevantes;
- aplique autorização no servidor;
- não confie apenas em dados enviados pelo cliente;
- use escopos e permissões quando necessário.

---

## 9.9 — O que pode dar errado?

Um erro grave é usar JWT sem validar assinatura. Nesse caso, qualquer pessoa poderia alterar o payload e tentar se passar por outro usuário.

Outro erro comum é colocar informações sensíveis no payload. O payload do JWT é codificado, não criptografado por padrão. Isso significa que ele pode ser lido por quem tiver acesso ao token.

Também é comum confundir autenticação com autorização. Saber quem é o usuário não basta. A API precisa verificar o que aquele usuário tem permissão para fazer.

---

## 9.10 — Resumo do capítulo

Autenticação identifica o usuário. Autorização define permissões. JWT é um formato de token composto por header, payload e assinatura. Tokens devem ser tratados como credenciais e validados com rigor no backend.

---

## 9.11 — Exercícios

1. Explique a diferença entre autenticação e autorização.
2. O que significa `Authorization: Bearer <token>`?
3. Quais são as três partes de um JWT?
4. Por que não devemos colocar senha dentro do payload de um JWT?

---

## 9.12 — Desafios

1. Desenhe o fluxo de login com JWT.
2. Liste três endpoints públicos e três endpoints protegidos de uma API.
3. Pesquise como o framework que você usa valida tokens JWT.

---

## 9.13 — Fixando o conhecimento

- Autenticação identifica.
- Autorização permite ou nega acesso.
- JWT possui header, payload e signature.
- Token deve ser enviado com cuidado.
- A validação real deve acontecer no servidor.

---

# Capítulo 10 — Padrões de integração: polling, webhooks e pushing

Nem toda integração precisa acontecer da mesma forma. Algumas informações podem ser consultadas de tempos em tempos. Outras precisam ser enviadas imediatamente quando um evento acontece.

Ao final deste capítulo, você será capaz de:

- explicar polling;
- explicar webhooks;
- entender pushing;
- escolher entre polling e webhook em cenários simples;
- reconhecer cuidados de segurança e confiabilidade em webhooks.

---

## 10.1 — O problema

Imagine um e-commerce esperando a confirmação de pagamento de um pedido. Como ele descobre que o pagamento foi aprovado?

Existem duas formas comuns:

1. o e-commerce pergunta várias vezes ao gateway se o pagamento foi aprovado;
2. o gateway avisa o e-commerce automaticamente quando o pagamento muda de status.

A primeira abordagem é polling. A segunda é webhook.

---

## 10.2 — Polling

No polling, o cliente faz requisições periódicas ao servidor para verificar se há novidades.

```http
GET /pagamentos/123/status
```

O cliente pode chamar esse endpoint a cada 10 segundos, por exemplo, até o status mudar.

Polling é simples de implementar, mas pode gerar muitas requisições desnecessárias. Se nada mudou, o cliente continua perguntando e o servidor continua respondendo.

---

## 10.3 — Webhooks

Webhooks funcionam com a ideia de evento. Quando algo acontece no servidor, ele envia uma requisição HTTP para uma URL configurada pelo cliente.

Exemplo:

```http
POST /webhooks/pagamentos
Content-Type: application/json

{
  "evento": "pagamento.aprovado",
  "pedidoId": "987",
  "pagamentoId": "123",
  "status": "aprovado"
}
```

---

## 10.4 — O que aconteceu no exemplo?

O gateway de pagamento enviou uma requisição `POST` para o endpoint de webhook do e-commerce. O body informa qual evento aconteceu e quais entidades estão envolvidas.

Nesse modelo, o e-commerce não precisa perguntar repetidamente. Ele apenas disponibiliza um endpoint público para receber notificações.

---

## 10.5 — Pushing

Pushing é o conceito geral de o servidor enviar informações de forma ativa para o cliente. Webhook é uma forma comum de pushing em integrações HTTP.

Outras tecnologias também podem implementar comportamento parecido, como WebSocket, Server-Sent Events e filas de mensagens, dependendo do tipo de sistema.

---

## 10.6 — Imagem comparativa

![Comparação entre webhook e polling](./images/api/webhookpolling.png)

*Figura 4 — Comparação entre polling e webhook. Fonte da imagem original: Level Up Coding.*

---

## 10.7 — Comparação prática

| Critério | Polling | Webhook |
|---|---|---|
| Quem inicia? | Cliente | Servidor |
| Complexidade inicial | Menor | Média |
| Tempo real | Limitado pelo intervalo | Mais próximo do tempo real |
| Uso de rede | Pode ser alto | Menor quando há poucos eventos |
| Requisito | Cliente precisa consultar | Cliente precisa expor endpoint público |

---

## 10.8 — Cuidados com webhooks

Webhooks precisam de cuidados para serem confiáveis:

- validar assinatura do evento;
- responder rapidamente;
- registrar eventos recebidos;
- tratar duplicidade com idempotência;
- implementar retries no emissor;
- não confiar cegamente no payload recebido;
- consultar a API oficial quando precisar confirmar dados críticos.

Um webhook pode ser enviado mais de uma vez. Por isso, o sistema receptor deve conseguir processar eventos repetidos sem causar efeitos duplicados.

---

## 10.9 — O que pode dar errado?

No polling, o problema comum é consultar com frequência alta demais. Isso pode sobrecarregar servidor, banco e rede.

Em webhooks, o problema comum é assumir que todo evento chegará uma única vez e na ordem correta. Sistemas reais podem reenviar eventos, entregar fora de ordem ou falhar temporariamente. Por isso, é importante ter logs, idempotência e validação.

---

## 10.10 — Resumo do capítulo

Polling é quando o cliente pergunta periodicamente por novidades. Webhook é quando o servidor envia uma notificação quando um evento acontece. Pushing é o conceito geral de envio ativo de dados do servidor para o cliente.

---

## 10.11 — Exercícios

1. Explique a diferença entre polling e webhook.
2. Em qual caso polling pode ser suficiente?
3. Em qual caso webhook é mais adequado?
4. Por que webhooks precisam de idempotência?

---

## 10.12 — Desafios

1. Desenhe o fluxo de pagamento aprovado usando polling.
2. Desenhe o mesmo fluxo usando webhook.
3. Crie um exemplo de payload de webhook para `pedido.cancelado`.

---

## 10.13 — Fixando o conhecimento

- Polling: cliente pergunta.
- Webhook: servidor avisa.
- Pushing: servidor envia ativamente.
- Webhooks exigem validação, logs, retries e idempotência.

---

# Capítulo 11 — Boas práticas para APIs profissionais

Depois de entender os conceitos principais, é importante saber como APIs reais costumam ser projetadas para serem legíveis, seguras e fáceis de manter.

Ao final deste capítulo, você será capaz de:

- reconhecer características de uma API bem projetada;
- aplicar boas práticas simples de versionamento, paginação e erros;
- entender cuidados básicos de segurança;
- pensar em documentação e manutenção.

---

## 11.1 — O problema

Uma API pode funcionar tecnicamente e ainda assim ser difícil de usar. Endpoints confusos, respostas inconsistentes, erros genéricos e falta de documentação tornam a integração mais lenta e aumentam bugs.

APIs profissionais precisam ser previsíveis.

---

## 11.2 — Nomeie recursos com clareza

Prefira substantivos no plural:

```http
GET /usuarios
GET /produtos
GET /pedidos
```

Evite misturar muitos verbos na URL:

```http
GET /buscarUsuarios
POST /criarProduto
GET /deletarPedido
```

A ação deve estar no método HTTP sempre que possível.

---

## 11.3 — Versionamento

APIs mudam com o tempo. Para evitar quebrar clientes antigos, muitas APIs usam versionamento.

Exemplo:

```http
GET /v1/usuarios
GET /v2/usuarios
```

Versionar não significa criar uma nova versão para qualquer pequena alteração. Mudanças compatíveis podem ser feitas na mesma versão. Mudanças que quebram contrato podem exigir uma nova versão.

---

## 11.4 — Paginação

Listagens grandes devem ser paginadas. Sem paginação, uma requisição pode tentar devolver milhares ou milhões de registros.

Exemplo:

```http
GET /usuarios?page=2&limit=20
```

Resposta possível:

```json
{
  "data": [
    { "id": 21, "nome": "Usuário 21" },
    { "id": 22, "nome": "Usuário 22" }
  ],
  "pagination": {
    "page": 2,
    "limit": 20,
    "total": 150
  }
}
```

---

## 11.5 — Filtros e ordenação

Filtros e ordenação costumam ser enviados por query parameters.

```http
GET /pedidos?status=pago&sort=-criadoEm
```

Nesse exemplo, a API lista pedidos pagos e ordena por data de criação decrescente, assumindo que o sinal `-` represente ordem decrescente na convenção da API.

O mais importante é documentar a convenção usada.

---

## 11.6 — Respostas de erro consistentes

Erros devem seguir um formato previsível.

```json
{
  "error": "resource_not_found",
  "message": "Usuário não encontrado.",
  "status": 404
}
```

Para validação:

```json
{
  "error": "validation_error",
  "message": "Existem campos inválidos.",
  "fields": [
    {
      "field": "email",
      "message": "Informe um e-mail válido."
    }
  ]
}
```

---

## 11.7 — Segurança básica

APIs devem ser projetadas pensando em segurança desde o início. Algumas práticas importantes:

- usar HTTPS;
- autenticar endpoints protegidos;
- aplicar autorização no servidor;
- validar entrada de dados;
- limitar taxa de requisições quando necessário;
- não expor stack trace em produção;
- registrar logs úteis sem vazar dados sensíveis;
- tratar tokens, senhas e chaves como segredo.

---

## 11.8 — Documentação

Uma API sem documentação obriga o consumidor a adivinhar como usá-la. Documente pelo menos:

- base URL;
- autenticação;
- endpoints;
- métodos HTTP;
- parâmetros;
- exemplos de request e response;
- status codes;
- erros comuns;
- limites de uso;
- versionamento.

Ferramentas como OpenAPI/Swagger ajudam a documentar contratos de APIs HTTP de forma padronizada.

---

## 11.9 — O que pode dar errado?

Um erro comum é pensar na API apenas do ponto de vista interno do backend. Uma boa API deve ser pensada também pelo ponto de vista de quem vai consumir. Se o consumidor precisa abrir o código do backend para entender como usar o endpoint, a API provavelmente está mal documentada.

Outro erro comum é alterar o comportamento de um endpoint sem considerar clientes existentes. APIs são contratos. Alterar campos, status codes ou regras sem aviso pode quebrar integrações.

---

## 11.10 — Resumo do capítulo

APIs profissionais são consistentes, seguras, documentadas e previsíveis. Bons nomes, status codes corretos, paginação, erros padronizados e autenticação bem implementada ajudam a criar APIs mais fáceis de usar e manter.

---

## 11.11 — Exercícios

1. Reescreva três endpoints ruins usando boas práticas REST.
2. Crie um exemplo de resposta de erro para validação de CPF inválido.
3. Crie uma rota paginada para listar pedidos.
4. Liste cinco informações que não podem faltar na documentação de uma API.

---

## 11.12 — Desafios

1. Escolha uma API pública e avalie se os endpoints são consistentes.
2. Crie um mini contrato de API para um sistema de tarefas.
3. Defina status codes para criação, busca, atualização, remoção e erro de validação.

---

## 11.13 — Fixando o conhecimento

- API é contrato.
- Consistência importa.
- Segurança deve ser pensada desde o início.
- Documentação reduz dúvidas e bugs.
- Mudanças incompatíveis exigem cuidado e versionamento.

---

# Referências bibliográficas

As referências abaixo foram usadas para revisar, complementar e validar os conceitos técnicos desta apostila.

- FIELDING, Roy Thomas. **Architectural Styles and the Design of Network-based Software Architectures**. University of California, Irvine, 2000. Disponível em: <https://roy.gbiv.com/pubs/dissertation/fielding_dissertation.pdf>. Acesso em: 31 maio 2026.
- FIELDING, R.; NOTTINGHAM, M.; RESCHKE, J. **RFC 9110: HTTP Semantics**. IETF, 2022. Disponível em: <https://www.rfc-editor.org/rfc/rfc9110.html>. Acesso em: 31 maio 2026.
- BRAY, T. **RFC 8259: The JavaScript Object Notation (JSON) Data Interchange Format**. IETF, 2017. Disponível em: <https://www.rfc-editor.org/rfc/rfc8259.html>. Acesso em: 31 maio 2026.
- JSON:API. **Specification v1.1**. Disponível em: <https://jsonapi.org/format/>. Acesso em: 31 maio 2026.
- MDN WEB DOCS. **Cross-Origin Resource Sharing (CORS)**. Disponível em: <https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS>. Acesso em: 31 maio 2026.
- JONES, M.; BRADLEY, J.; SAKIMURA, N. **RFC 7519: JSON Web Token (JWT)**. IETF, 2015. Disponível em: <https://www.rfc-editor.org/rfc/rfc7519.html>. Acesso em: 31 maio 2026.
- HARDT, D. **RFC 6749: The OAuth 2.0 Authorization Framework**. IETF, 2012. Disponível em: <https://datatracker.ietf.org/doc/html/rfc6749>. Acesso em: 31 maio 2026.
- GRAPHQL FOUNDATION. **GraphQL Learn**. Disponível em: <https://graphql.org/learn/>. Acesso em: 31 maio 2026.
- GRPC AUTHORS. **Introduction to gRPC**. Disponível em: <https://grpc.io/docs/what-is-grpc/introduction/>. Acesso em: 31 maio 2026.
- W3C. **SOAP Specifications**. Disponível em: <https://www.w3.org/TR/soap/>. Acesso em: 31 maio 2026.
- FETTE, I.; MELNIKOV, A. **RFC 6455: The WebSocket Protocol**. IETF, 2011. Disponível em: <https://www.rfc-editor.org/rfc/rfc6455>. Acesso em: 31 maio 2026.
- STENBERG, Daniel et al. **curl: command line tool and library for transferring data with URLs**. Disponível em: <https://curl.se/docs/manpage.html>. Acesso em: 31 maio 2026.
- OWASP. **OWASP API Security Top 10 — 2023**. Disponível em: <https://owasp.org/API-Security/editions/2023/en/0x11-t10/>. Acesso em: 31 maio 2026.
- OPENAPI INITIATIVE. **OpenAPI Specification**. Disponível em: <https://spec.openapis.org/oas/latest.html>. Acesso em: 31 maio 2026.
- GITHUB DOCS. **About webhooks**. Disponível em: <https://docs.github.com/en/webhooks/about-webhooks>. Acesso em: 31 maio 2026.
- CLOUDFLARE. **Client-side vs server-side**. Disponível em: <https://www.cloudflare.com/learning/serverless/glossary/client-side-vs-server-side/>. Acesso em: 31 maio 2026.
- JWT.IO. **JSON Web Tokens**. Disponível em: <https://jwt.io/>. Acesso em: 31 maio 2026.
- ROADMAP.SH. **JWT Authentication**. Disponível em: <https://roadmap.sh/guides/jwt-authentication>. Acesso em: 31 maio 2026.
- LEVEL UP CODING. **Webhook vs Polling**. Publicação em X. Disponível em: <https://x.com/LevelUpCoding_/status/1809118819966988592>. Acesso em: 31 maio 2026.

