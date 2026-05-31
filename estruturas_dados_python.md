# Estruturas de Dados em Python

## Sobre esta apostila

Esta apostila apresenta as principais estruturas de dados nativas do Python: listas, tuplas, conjuntos e dicionários. O objetivo é explicar cada estrutura de forma prática, mostrando quando usar, como manipular os dados, quais erros são comuns e quais cuidados tomar em situações reais de desenvolvimento.

O conteúdo foi organizado para ser lido de forma progressiva. Primeiro veremos o problema que as estruturas de dados resolvem, depois estudaremos cada estrutura separadamente e, por fim, compararemos quando escolher cada uma.

> Observação sobre imagens: o documento original não possui imagens incorporadas. Caso esta apostila receba imagens futuramente, utilize o padrão de caminho relativo `./images/estruturas-dados-python/nome-da-imagem.png`, mantendo todas as imagens em uma pasta específica para este material.

## Como estudar por esta apostila

Leia os capítulos na ordem, execute os exemplos em um arquivo `.py` ou em um notebook e altere os valores para observar o comportamento do Python. Não apenas copie os códigos: tente prever a saída antes de executar. Isso ajuda a fixar conceitos como mutabilidade, índice, cópia rasa, cópia profunda e busca por chave.

Os exemplos são curtos de propósito. A ideia é entender o conceito isolado antes de aplicar em projetos maiores, como APIs, automações, análise de dados ou sistemas backend.

## Índice

1. [Capítulo 1 — Por que estudar estruturas de dados?](#capítulo-1--por-que-estudar-estruturas-de-dados)
2. [Capítulo 2 — Listas](#capítulo-2--listas)
3. [Capítulo 3 — Tuplas e operações de sequência](#capítulo-3--tuplas-e-operações-de-sequência)
4. [Capítulo 4 — Conjuntos](#capítulo-4--conjuntos)
5. [Capítulo 5 — Dicionários](#capítulo-5--dicionários)
6. [Capítulo 6 — Escolhendo a estrutura correta](#capítulo-6--escolhendo-a-estrutura-correta)
7. [Referências bibliográficas](#referências-bibliográficas)

---

# Capítulo 1 — Por que estudar estruturas de dados?

Estruturas de dados são formas de organizar informações dentro de um programa. Em Python, muitas tarefas do dia a dia envolvem guardar, consultar, transformar, filtrar ou agrupar dados. Para isso, usamos estruturas como listas, tuplas, conjuntos e dicionários.

Ao final deste capítulo, você será capaz de:

- entender o que são estruturas de dados;
- diferenciar coleções mutáveis e imutáveis;
- entender a diferença entre estruturas ordenadas e não ordenadas;
- identificar quando uma estrutura é mais adequada que outra.

## 1.1 — O problema

Imagine que você está desenvolvendo uma API e precisa guardar informações temporárias sobre usuários, produtos, permissões ou tarefas. Dependendo do caso, você pode precisar:

- manter os dados em uma ordem específica;
- evitar valores duplicados;
- buscar informações rapidamente por uma chave;
- impedir que um conjunto de valores seja alterado acidentalmente;
- transformar uma coleção de dados em outra.

Se você escolher a estrutura errada, o código pode ficar mais difícil de entender, mais lento ou mais propenso a erros. Por exemplo: se você precisa verificar se um item já existe em uma coleção enorme, usar uma lista pode ser pior do que usar um conjunto. Se você precisa buscar dados pelo identificador de um usuário, um dicionário costuma ser mais adequado do que uma lista.

## 1.2 — O que são coleções em Python?

Coleções são objetos que armazenam múltiplos valores. As principais coleções nativas usadas nesta apostila são:

| Estrutura | Exemplo | Característica principal |
|---|---|---|
| Lista | `[1, 2, 3]` | Ordenada e mutável |
| Tupla | `(1, 2, 3)` | Ordenada e imutável |
| Conjunto | `{1, 2, 3}` | Não possui elementos repetidos |
| Dicionário | `{"nome": "Ana"}` | Guarda pares chave-valor |

Essas estruturas podem armazenar valores simples, como números e strings, ou valores compostos, como outras listas, tuplas e dicionários.

## 1.3 — Mutabilidade

Mutabilidade indica se um objeto pode ser alterado depois de criado.

Listas, conjuntos e dicionários são mutáveis. Isso significa que podemos adicionar, remover ou alterar seus elementos.

```python
nomes = ["Ana", "Bruno"]
nomes.append("Carla")
print(nomes)
```

Saída esperada:

```text
['Ana', 'Bruno', 'Carla']
```

Tuplas são imutáveis. Depois de criada, não é possível trocar diretamente um item de posição.

```python
coordenadas = (10, 20)
coordenadas[0] = 99
```

Esse código gera erro, porque a tupla não permite alteração direta de seus elementos.

## 1.4 — Ordenação

Quando dizemos que uma estrutura é ordenada, queremos dizer que ela preserva uma sequência de elementos e permite acesso por índice.

Listas e tuplas são ordenadas:

```python
valores = [10, 20, 30]
print(valores[0])
print(valores[2])
```

Saída esperada:

```text
10
30
```

Conjuntos não devem ser usados quando a ordem importa. Eles servem principalmente para representar valores únicos e realizar operações matemáticas como união, interseção e diferença.

Dicionários preservam a ordem de inserção nas versões modernas do Python, mas sua principal finalidade não é acessar por posição, e sim por chave.

## 1.5 — O que é realmente importante?

No começo, o mais importante não é decorar todos os métodos, mas entender a finalidade de cada estrutura:

- use lista quando a ordem importa e os dados podem mudar;
- use tupla quando a ordem importa, mas os dados devem representar um conjunto fixo de valores;
- use conjunto quando você precisa remover duplicidades ou testar pertencimento de forma eficiente;
- use dicionário quando precisa associar uma chave a um valor.

## 1.6 — Resumo do capítulo

Neste capítulo, vimos que estruturas de dados são formas de organizar valores dentro do programa. Também vimos que cada estrutura tem uma finalidade própria. A escolha correta ajuda o código a ficar mais simples, legível e eficiente.

## 1.7 — Exercícios

1. Crie uma lista com três nomes de linguagens de programação.
2. Crie uma tupla representando uma coordenada com dois valores.
3. Crie um conjunto com números repetidos e observe o resultado.
4. Crie um dicionário representando uma pessoa, contendo nome, idade e cidade.

## 1.8 — Fixando o conhecimento

1. Qual estrutura você usaria para guardar uma lista de tarefas?
2. Qual estrutura você usaria para guardar coordenadas que não devem ser alteradas?
3. Qual estrutura você usaria para remover valores duplicados?
4. Qual estrutura você usaria para buscar um usuário pelo ID?

---

# Capítulo 2 — Listas

Listas são uma das estruturas mais usadas em Python. Elas permitem armazenar vários elementos em uma única variável, preservam a ordem de inserção e podem ser modificadas depois de criadas.

Ao final deste capítulo, você será capaz de:

- criar e acessar listas;
- adicionar, remover, buscar e ordenar elementos;
- diferenciar operações `in-place` de operações que criam nova lista;
- entender cópia rasa e cópia profunda;
- usar list comprehensions para transformar e filtrar dados.

## 2.1 — O problema

Imagine que você está criando um sistema simples de tarefas. O usuário pode adicionar uma nova tarefa, remover uma tarefa concluída, ordenar tarefas por nome e consultar se uma tarefa já existe.

Esse tipo de problema exige uma estrutura que possa crescer, diminuir e preservar a ordem dos elementos. A lista resolve exatamente esse cenário.

## 2.2 — O que é uma lista?

Uma lista é uma coleção ordenada e mutável. Ela pode armazenar elementos de tipos diferentes, embora em código profissional seja comum manter itens do mesmo tipo para facilitar a leitura e evitar erros.

```python
lista = [1, 2, 3]

minha_lista = [
    42,
    "Python",
    3.14,
    [1, 2, 3],
    {"chave": "valor"},
]
```

No primeiro exemplo, temos uma lista simples de números. No segundo, a lista mistura inteiro, string, número decimal, outra lista e um dicionário.

## 2.3 — Características das listas

As listas possuem quatro características importantes:

| Característica | Explicação |
|---|---|
| Mutáveis | Permitem adicionar, remover e modificar elementos |
| Ordenadas | Mantêm a sequência dos elementos |
| Tamanho dinâmico | Podem crescer e diminuir durante a execução |
| Indexadas | Cada elemento pode ser acessado pela posição |

Exemplo:

```python
notas = [5, 6, 6]
media = sum(notas) / len(notas)

print(f"Média das notas: {media}")
```

Saída esperada:

```text
Média das notas: 5.666666666666667
```

O código usa `sum(notas)` para somar todos os valores e `len(notas)` para contar quantos elementos existem. Depois, divide a soma pela quantidade de notas.

## 2.4 — Adicionando elementos

Vamos usar a seguinte lista como exemplo:

```python
tarefas = ["Revisar código", "Atualizar documentação", "Testar novo endpoint"]
```

### 2.4.1 — `.append(item)`

O método `append()` adiciona um único item ao final da lista.

```python
tarefas = ["Revisar código", "Atualizar documentação", "Testar novo endpoint"]

tarefas.append("Deploy em staging")

print(tarefas)
```

Saída esperada:

```text
['Revisar código', 'Atualizar documentação', 'Testar novo endpoint', 'Deploy em staging']
```

O `append()` modifica a lista original. Esse tipo de operação é chamado de operação `in-place`, porque a alteração acontece no próprio objeto.

### 2.4.2 — `.extend(iterável)`

O método `extend()` adiciona vários elementos ao final da lista. Ele recebe um iterável, como uma lista, tupla ou string, e adiciona cada item separadamente.

```python
tarefas = ["Revisar código", "Atualizar documentação"]

tarefas.extend(["Testar API", "Fazer deploy"])

print(tarefas)
```

Saída esperada:

```text
['Revisar código', 'Atualizar documentação', 'Testar API', 'Fazer deploy']
```

### 2.4.3 — `append()` vs `extend()`

A diferença entre `append()` e `extend()` é uma das dúvidas mais comuns no início.

```python
lista = [1, 2, 3]
lista.append([4, 5])

print(lista)
```

Saída esperada:

```text
[1, 2, 3, [4, 5]]
```

Nesse caso, `[4, 5]` entrou como um único elemento dentro da lista.

Agora observe o `extend()`:

```python
lista = [1, 2, 3]
lista.extend([4, 5])

print(lista)
```

Saída esperada:

```text
[1, 2, 3, 4, 5]
```

Aqui, os elementos `4` e `5` foram adicionados individualmente.

Use `append()` quando quiser adicionar um item inteiro. Use `extend()` quando quiser juntar os elementos de outro iterável à lista atual.

### 2.4.4 — `.insert(índice, item)`

O método `insert()` insere um item em uma posição específica, deslocando os elementos seguintes para a direita.

```python
tarefas = ["Revisar código", "Atualizar documentação"]

tarefas.insert(1, "Corrigir bug #123")

print(tarefas)
```

Saída esperada:

```text
['Revisar código', 'Corrigir bug #123', 'Atualizar documentação']
```

O item foi inserido no índice `1`, ou seja, na segunda posição. O elemento que estava nessa posição foi deslocado para frente.

Em listas grandes, inserir no meio ou no começo tende a ser mais custoso do que adicionar ao final, porque vários elementos precisam ser deslocados.

## 2.5 — Removendo elementos

### 2.5.1 — `.remove(item)`

O método `remove()` remove a primeira ocorrência de um valor.

```python
tarefas = [
    "Revisar código",
    "Atualizar documentação",
    "Testar novo endpoint",
]

tarefas.remove("Atualizar documentação")

print(tarefas)
```

Saída esperada:

```text
['Revisar código', 'Testar novo endpoint']
```

Se o valor não existir, o Python gera `ValueError`.

```python
tarefas = ["Revisar código", "Testar novo endpoint"]

tarefas.remove("Deploy")
```

Erro esperado:

```text
ValueError: list.remove(x): x not in list
```

Para evitar esse erro, verifique antes:

```python
if "Deploy" in tarefas:
    tarefas.remove("Deploy")
```

### 2.5.2 — `.pop(índice)`

O método `pop()` remove e retorna o elemento de uma posição. Se nenhum índice for informado, ele remove o último item.

```python
tarefas = ["Revisar código", "Corrigir bug #123", "Testar novo endpoint"]

tarefa_removida = tarefas.pop(0)

print(f"Tarefa removida: {tarefa_removida}")
print(tarefas)
```

Saída esperada:

```text
Tarefa removida: Revisar código
['Corrigir bug #123', 'Testar novo endpoint']
```

Removendo o último item:

```python
tarefas = ["Corrigir bug #123", "Testar novo endpoint", "Deploy em staging"]

ultima_tarefa = tarefas.pop()

print(f"Última tarefa removida: {ultima_tarefa}")
print(tarefas)
```

Saída esperada:

```text
Última tarefa removida: Deploy em staging
['Corrigir bug #123', 'Testar novo endpoint']
```

Use `pop()` quando você precisa remover e também usar o valor removido.

### 2.5.3 — `.clear()`

O método `clear()` remove todos os elementos da lista.

```python
tarefas = ["Corrigir bug", "Testar API", "Fazer deploy"]

tarefas.clear()

print(tarefas)
```

Saída esperada:

```text
[]
```

Depois do `clear()`, a lista continua existindo, mas está vazia.

## 2.6 — Buscando elementos

### 2.6.1 — `.index(valor, start=0, end=len(lista))`

O método `index()` retorna o índice da primeira ocorrência de um valor.

```python
frutas = ["maçã", "banana", "uva", "banana"]

print(frutas.index("banana"))
print(frutas.index("banana", 2))
```

Saída esperada:

```text
1
3
```

Na primeira busca, o Python encontra `"banana"` no índice `1`. Na segunda busca, ele começa a procurar a partir do índice `2`, por isso encontra a próxima ocorrência no índice `3`.

Se o valor não existir, `index()` gera `ValueError`.

```python
frutas = ["maçã", "banana", "uva"]

print(frutas.index("pera"))
```

Para evitar erro:

```python
if "pera" in frutas:
    print(frutas.index("pera"))
else:
    print("Fruta não encontrada")
```

### 2.6.2 — `.count(valor)`

O método `count()` retorna quantas vezes um valor aparece na lista.

```python
frutas = ["maçã", "banana", "uva", "banana"]

print(frutas.count("banana"))
print(frutas.count("pera"))
```

Saída esperada:

```text
2
0
```

Esse método é útil quando você precisa contar ocorrências simples. Para contagens mais complexas, também é comum usar `collections.Counter`, mas esse assunto fica para um estudo posterior.

## 2.7 — Copiando listas

Em Python, é muito importante diferenciar atribuição de cópia.

### 2.7.1 — Atribuição não copia

Quando fazemos `b = a`, não criamos uma nova lista. Criamos apenas uma nova referência para a mesma lista.

```python
a = [1, 2, 3]
b = a

b.append(4)

print(a)
print(b)
```

Saída esperada:

```text
[1, 2, 3, 4]
[1, 2, 3, 4]
```

Como `a` e `b` apontam para o mesmo objeto, modificar `b` também afeta `a`.

### 2.7.2 — `.copy()` e cópia rasa

O método `copy()` cria uma nova lista externa.

```python
a = [1, 2, 3]
c = a.copy()

c.append(4)

print(a)
print(c)
```

Saída esperada:

```text
[1, 2, 3]
[1, 2, 3, 4]
```

Nesse exemplo, `c` é uma nova lista. Alterar `c` não altera `a`.

### 2.7.3 — Cópia rasa com lista aninhada

A cópia rasa copia apenas a lista externa. Se houver listas internas, elas continuam sendo compartilhadas.

```python
original = [[1, 2], [3, 4]]
shallow = original.copy()

shallow[0].append(99)

print(original)
print(shallow)
```

Saída esperada:

```text
[[1, 2, 99], [3, 4]]
[[1, 2, 99], [3, 4]]
```

Mesmo usando `copy()`, a sublista `[1, 2]` continuou sendo a mesma nos dois objetos. Por isso, alterar essa sublista afeta as duas estruturas.

### 2.7.4 — `deepcopy()` e cópia profunda

Para copiar também os objetos internos, usamos `copy.deepcopy()`.

```python
import copy

original = [[1, 2], [3, 4]]
deep = copy.deepcopy(original)

deep[0].append(100)

print(original)
print(deep)
```

Saída esperada:

```text
[[1, 2], [3, 4]]
[[1, 2, 100], [3, 4]]
```

A cópia profunda é útil quando você precisa duplicar uma estrutura composta sem compartilhar objetos internos. Porém, ela pode ser mais custosa, então deve ser usada quando realmente necessário.

## 2.8 — Ordenando elementos

### 2.8.1 — `.sort()`

O método `sort()` ordena a própria lista. Ele é `in-place`, ou seja, não cria uma nova lista.

```python
tarefas = ["Testar API", "Corrigir bug", "Atualizar docs", "Refatorar código"]

tarefas.sort()

print(tarefas)
```

Saída esperada:

```text
['Atualizar docs', 'Corrigir bug', 'Refatorar código', 'Testar API']
```

Ordenando em ordem decrescente:

```python
tarefas.sort(reverse=True)

print(tarefas)
```

Saída esperada:

```text
['Testar API', 'Refatorar código', 'Corrigir bug', 'Atualizar docs']
```

### 2.8.2 — `sorted()`

A função `sorted()` retorna uma nova lista ordenada e mantém a original intacta.

```python
tarefas = ["Testar API", "Corrigir bug", "Atualizar docs", "Refatorar código"]

tarefas_ordenadas = sorted(tarefas)

print(tarefas)
print(tarefas_ordenadas)
```

Saída esperada:

```text
['Testar API', 'Corrigir bug', 'Atualizar docs', 'Refatorar código']
['Atualizar docs', 'Corrigir bug', 'Refatorar código', 'Testar API']
```

Use `sort()` quando você quer modificar a lista original. Use `sorted()` quando precisa de uma versão ordenada sem alterar a lista original.

### 2.8.3 — `.reverse()`

O método `reverse()` inverte a ordem atual da lista `in-place`.

```python
tarefas = ["Atualizar docs", "Corrigir bug", "Refatorar código", "Testar API"]

tarefas.reverse()

print(tarefas)
```

Saída esperada:

```text
['Testar API', 'Refatorar código', 'Corrigir bug', 'Atualizar docs']
```

Importante: `reverse()` não ordena. Ele apenas inverte a ordem que já existe.

## 2.9 — List comprehensions

List comprehensions são usadas para criar novas listas a partir de uma sequência existente. Elas são úteis quando queremos transformar ou filtrar dados de forma curta e legível.

A estrutura geral é:

```python
nova_lista = [expressao for item in iteravel]
```

Vamos usar uma lista de produtos:

```python
produtos = [
    ["Arroz", 5.99, 30],
    ["Feijão", 8.49, 20],
    ["Macarrão", 4.79, 50],
]
```

Cada produto é representado no formato `[nome, preco, quantidade]`.

### 2.9.1 — Versão com `for` tradicional

```python
precos = []

for produto in produtos:
    precos.append(produto[1])

print(precos)
```

Saída esperada:

```text
[5.99, 8.49, 4.79]
```

O código começa com uma lista vazia e adiciona o preço de cada produto.

### 2.9.2 — Versão com list comprehension

```python
precos = [produto[1] for produto in produtos]

print(precos)
```

Saída esperada:

```text
[5.99, 8.49, 4.79]
```

A list comprehension faz a mesma coisa, mas em uma única expressão.

### 2.9.3 — Somando valores

```python
soma_precos = sum([produto[1] for produto in produtos])

print(f"A soma dos preços é: R$ {soma_precos:.2f}")
```

Saída esperada:

```text
A soma dos preços é: R$ 19.27
```

Nesse caso, a list comprehension cria uma lista de preços e `sum()` soma todos os valores.

Também poderíamos escrever sem criar a lista intermediária, usando uma expressão geradora:

```python
soma_precos = sum(produto[1] for produto in produtos)
```

Essa forma costuma ser melhor quando você só precisa iterar pelos valores uma vez.

### 2.9.4 — Condicional ternário

Antes de usar `if/else` dentro de uma list comprehension, vale entender o condicional ternário.

A estrutura é:

```python
valor_se_verdadeiro if condicao else valor_se_falso
```

Exemplo:

```python
idade = 20
status = "Permitido" if idade >= 18 else "Negado"

print(status)
```

Saída esperada:

```text
Permitido
```

### 2.9.5 — List comprehension com `if/else`

```python
produtos = [
    ["Arroz", 5.99, 30],
    ["Feijão", 8.49, 20],
    ["Macarrão", 4.79, 50],
]

status_estoque = [
    [produto[0], "Estoque baixo" if produto[2] < 30 else "Estoque OK"]
    for produto in produtos
]

print(status_estoque)
```

Saída esperada:

```text
[['Arroz', 'Estoque OK'], ['Feijão', 'Estoque baixo'], ['Macarrão', 'Estoque OK']]
```

A expressão percorre os produtos e cria uma nova lista com o nome do produto e o status do estoque. Se a quantidade for menor que `30`, o status será `"Estoque baixo"`; caso contrário, será `"Estoque OK"`.

## 2.10 — O que pode dar errado?

### Modificar uma lista achando que criou uma cópia

```python
a = [1, 2, 3]
b = a
b.append(4)

print(a)
```

O valor de `a` também muda, porque `b` aponta para a mesma lista.

### Usar `append()` quando queria `extend()`

```python
valores = [1, 2, 3]
valores.append([4, 5])

print(valores)
```

Saída:

```text
[1, 2, 3, [4, 5]]
```

Se a intenção era juntar os elementos, o correto seria `extend()`.

### Chamar `remove()` em um item inexistente

```python
nomes = ["Ana", "Bruno"]
nomes.remove("Carla")
```

Esse código gera `ValueError`. Verifique antes com `in` quando houver dúvida.

## 2.11 — Um pouco mais

Listas são excelentes para coleções ordenadas e mutáveis, mas nem sempre são a melhor escolha. Se você precisa verificar pertencimento com frequência, um conjunto pode ser melhor. Se você precisa associar identificadores a dados, um dicionário é mais apropriado.

Também é importante evitar listas muito complexas, como listas de listas com muitas colunas difíceis de lembrar. Em um sistema real, uma lista de dicionários costuma ser mais legível:

```python
produtos = [
    {"nome": "Arroz", "preco": 5.99, "quantidade": 30},
    {"nome": "Feijão", "preco": 8.49, "quantidade": 20},
]

print(produtos[0]["nome"])
```

Essa estrutura deixa claro o significado de cada campo.

## 2.12 — Resumo do capítulo

Listas são coleções ordenadas, mutáveis e muito flexíveis. Elas permitem adicionar, remover, buscar, ordenar e transformar dados. O ponto mais importante é entender quando uma operação modifica a lista original e quando cria uma nova lista.

## 2.13 — Exercícios

1. Crie uma lista de tarefas e adicione uma nova tarefa com `append()`.
2. Adicione três novas tarefas usando `extend()`.
3. Insira uma tarefa urgente no índice `0` usando `insert()`.
4. Remova uma tarefa pelo valor usando `remove()`.
5. Remova a última tarefa com `pop()` e imprima o valor removido.
6. Crie uma lista de números e gere uma nova lista contendo o dobro de cada número usando list comprehension.

## 2.14 — Desafios

1. Dada uma lista de produtos no formato `[nome, preco, quantidade]`, gere uma nova lista apenas com os produtos que possuem estoque menor que `30`.
2. Crie uma lista de notas e calcule a média.
3. Faça uma cópia rasa de uma lista aninhada e mostre, com código, por que alterar uma sublista afeta a original.
4. Refaça o desafio anterior usando `copy.deepcopy()`.

## 2.15 — Fixando o conhecimento

1. Qual é a diferença entre `append()` e `extend()`?
2. Qual é a diferença entre `sort()` e `sorted()`?
3. O que acontece quando fazemos `b = a` com listas?
4. Quando uma cópia profunda é necessária?

---

# Capítulo 3 — Tuplas e operações de sequência

Tuplas são coleções ordenadas e imutáveis. Elas são parecidas com listas em alguns aspectos, mas têm uma diferença fundamental: depois de criada, a tupla não deve ser modificada.

Ao final deste capítulo, você será capaz de:

- criar e acessar tuplas;
- entender por que tuplas são imutáveis;
- usar os métodos `count()` e `index()`;
- aplicar operações comuns de sequência, como índice, slicing, `in`, iteração, desempacotamento e concatenação.

## 3.1 — O problema

Alguns dados representam uma composição fixa. Por exemplo:

- coordenadas `(x, y)`;
- data no formato `(ano, mes, dia)`;
- par de valores `(chave, valor)`;
- retorno múltiplo de uma função.

Nesses casos, muitas vezes não queremos uma estrutura que fique mudando de tamanho. Queremos apenas agrupar valores relacionados. A tupla é uma boa escolha para isso.

## 3.2 — O que é uma tupla?

Uma tupla é uma coleção ordenada e imutável.

```python
minha_tupla = (42, "Python", 3.14)
```

Também é possível ter uma tupla com diferentes tipos:

```python
minha_tupla = (42, "Python", 3.14, [1, 2, 3], {"chave": "valor"})
```

Apesar de isso ser permitido, misturar muitos tipos pode deixar o código mais difícil de entender. Use com cuidado.

## 3.3 — Características das tuplas

| Característica | Explicação |
|---|---|
| Imutáveis | Não permitem alterar diretamente seus elementos |
| Ordenadas | Mantêm a posição dos elementos |
| Indexadas | Permitem acesso por índice |
| Leves para representar grupos fixos | São boas para dados que não devem mudar |

Exemplo:

```python
coordenadas = (10, 20)

print(coordenadas[0])
print(coordenadas[1])
```

Saída esperada:

```text
10
20
```

## 3.4 — Atenção: tupla imutável com objeto mutável dentro

Uma tupla não permite trocar diretamente seus elementos, mas se ela contiver um objeto mutável, como uma lista, esse objeto interno ainda pode ser alterado.

```python
dados = ("Ana", [10, 8, 9])

dados[1].append(7)

print(dados)
```

Saída esperada:

```text
('Ana', [10, 8, 9, 7])
```

A tupla continua contendo os mesmos dois elementos: a string `"Ana"` e a mesma lista de notas. O que mudou foi o conteúdo da lista interna.

## 3.5 — Métodos de tuplas

Tuplas possuem poucos métodos, justamente porque são imutáveis.

### 3.5.1 — `.count(valor)`

O método `count()` retorna quantas vezes um valor aparece na tupla.

```python
tupla = (1, 2, 2, 3, 2)
qtd = tupla.count(2)

print(qtd)
```

Saída esperada:

```text
3
```

### 3.5.2 — `.index(valor, start, stop)`

O método `index()` retorna o índice da primeira ocorrência de um valor.

```python
tupla = ("a", "b", "c", "b")
pos = tupla.index("b")

print(pos)
```

Saída esperada:

```text
1
```

Também é possível informar de onde a busca deve começar:

```python
tupla = ("a", "b", "c", "b")
pos = tupla.index("b", 2)

print(pos)
```

Saída esperada:

```text
3
```

Como a busca começa no índice `2`, o Python ignora o primeiro `"b"` e encontra a ocorrência seguinte.

## 3.6 — Operações em listas e tuplas

Listas e tuplas são sequências. Por isso, compartilham várias operações.

## 3.7 — Acesso por índice

O acesso por índice permite recuperar um elemento pela posição.

```python
lista = [10, 20, 30]
print(lista[1])

tupla = (11, 42, 64)
print(tupla[1])
```

Saída esperada:

```text
20
42
```

A contagem começa em zero. Por isso, o índice `1` representa o segundo elemento.

Também é possível usar índices negativos:

```python
valores = [10, 20, 30]

print(valores[-1])
print(valores[-2])
```

Saída esperada:

```text
30
20
```

O índice `-1` acessa o último elemento, `-2` acessa o penúltimo, e assim por diante.

## 3.8 — Slicing

Slicing, ou fatiamento, permite extrair uma parte da sequência.

A estrutura é:

```python
sequencia[start:stop]
```

O índice `start` é incluído. O índice `stop` é excluído.

```python
lista = [10, 20, 30, 40]
print(lista[1:3])

tupla = (10, 20, 30, 40)
print(tupla[1:3])
```

Saída esperada:

```text
[20, 30]
(20, 30)
```

O Python começa no índice `1` e vai até antes do índice `3`.

## 3.9 — Slicing com step

O slicing também pode receber um terceiro parâmetro, chamado `step`.

```python
sequencia[start:stop:step]
```

O `step` define de quantos em quantos índices a sequência será percorrida.

```python
lista = [10, 20, 30, 40, 50, 60]

print(lista[::2])
```

Saída esperada:

```text
[10, 30, 50]
```

Como o `step` é `2`, a lista é percorrida de dois em dois elementos.

### 3.9.1 — Invertendo uma sequência com step negativo

```python
lista = [10, 20, 30, 40]

print(lista[::-1])
```

Saída esperada:

```text
[40, 30, 20, 10]
```

O `step` `-1` percorre a sequência de trás para frente.

### 3.9.2 — Usando start, stop e step juntos

```python
lista = [0, 1, 2, 3, 4, 5, 6, 7]

print(lista[1:7:2])
```

Saída esperada:

```text
[1, 3, 5]
```

O slicing começa no índice `1`, vai até antes do índice `7` e avança de dois em dois.

## 3.10 — Operador `in`

O operador `in` verifica se um valor existe dentro de uma sequência.

```python
lista = [10, 20, 30]

print(20 in lista)
print(40 in lista)
```

Saída esperada:

```text
True
False
```

Também funciona com tuplas:

```python
tupla = (10, 20, 30)

print(20 in tupla)
print(40 in tupla)
```

Saída esperada:

```text
True
False
```

Em listas e tuplas, a busca com `in` pode precisar percorrer vários elementos até encontrar o valor. Por isso, em coleções muito grandes, conjuntos e dicionários podem ser melhores para testes frequentes de pertencimento.

## 3.11 — Iteração

Iterar significa percorrer os elementos de uma sequência um por um.

```python
linguagens = ["Python", "Java", "Go"]

for linguagem in linguagens:
    print(linguagem)
```

Saída esperada:

```text
Python
Java
Go
```

Quando também precisamos do índice, usamos `enumerate()`:

```python
linguagens = ["Python", "Java", "Go"]

for indice, linguagem in enumerate(linguagens):
    print(indice, linguagem)
```

Saída esperada:

```text
0 Python
1 Java
2 Go
```

## 3.12 — Desempacotamento de sequências

O desempacotamento permite distribuir os itens de uma sequência em variáveis individuais.

```python
coordenadas = (10, 20, 30)
x, y, z = coordenadas

print(f"x={x}, y={y}, z={z}")
```

Saída esperada:

```text
x=10, y=20, z=30
```

Também funciona com listas:

```python
dados = [10, 20, 30]
a, b, c = dados

print(f"a={a}, b={b}, c={c}")
```

Saída esperada:

```text
a=10, b=20, c=30
```

A quantidade de variáveis precisa bater com a quantidade de elementos. Caso contrário, o Python gera erro.

## 3.13 — Desempacotamento estendido

O operador `*` permite capturar múltiplos itens restantes em uma nova lista.

```python
numeros = [1, 2, 3, 4, 5]

primeiro, *meio, ultimo = numeros

print(primeiro)
print(meio)
print(ultimo)
```

Saída esperada:

```text
1
[2, 3, 4]
5
```

Esse recurso é útil quando queremos separar começo, meio e fim de uma sequência sem saber exatamente quantos elementos estão no meio.

## 3.14 — Concatenação de listas e tuplas

Listas e tuplas suportam o operador `+` para concatenação.

```python
tupla1 = (1, 2)
tupla2 = (3, 4)

nova_tupla = tupla1 + tupla2

print(nova_tupla)
```

Saída esperada:

```text
(1, 2, 3, 4)
```

Com listas:

```python
lista1 = [1, 2]
lista2 = [3, 4]

nova_lista = lista1 + lista2

print(nova_lista)
```

Saída esperada:

```text
[1, 2, 3, 4]
```

O operador `+` cria uma nova sequência. Ele não modifica diretamente as sequências originais.

## 3.15 — O que pode dar errado?

### Tentar alterar uma tupla diretamente

```python
coordenadas = (10, 20)
coordenadas[0] = 99
```

Esse código gera `TypeError`, porque tuplas são imutáveis.

### Confundir tupla de um elemento com valor entre parênteses

```python
a = (10)
b = (10,)

print(type(a))
print(type(b))
```

Saída esperada:

```text
<class 'int'>
<class 'tuple'>
```

A vírgula é o que define a tupla de um único elemento.

### Desempacotar com quantidade errada de variáveis

```python
valores = (1, 2, 3)
a, b = valores
```

Esse código gera erro porque existem três valores e apenas duas variáveis.

## 3.16 — Um pouco mais

Tuplas são muito usadas em retornos múltiplos de funções.

```python
def calcular_extremos(numeros):
    return min(numeros), max(numeros)

menor, maior = calcular_extremos([10, 5, 30, 2])

print(menor)
print(maior)
```

Saída esperada:

```text
2
30
```

A função retorna dois valores. Na prática, o Python retorna uma tupla, que depois é desempacotada em `menor` e `maior`.

## 3.17 — Resumo do capítulo

Tuplas são estruturas ordenadas e imutáveis, indicadas para agrupar valores que representam uma composição fixa. Listas e tuplas compartilham operações de sequência, como acesso por índice, slicing, `in`, iteração, concatenação e desempacotamento.

## 3.18 — Exercícios

1. Crie uma tupla com três linguagens de programação.
2. Acesse o primeiro e o último elemento da tupla.
3. Use `count()` para contar quantas vezes um valor aparece.
4. Use `index()` para descobrir a posição de um valor.
5. Crie uma lista de números e inverta a ordem usando slicing.
6. Faça o desempacotamento de uma tupla com três valores.

## 3.19 — Desafios

1. Crie uma função que receba uma lista de números e retorne uma tupla com o menor, o maior e a média.
2. Dada uma lista com cinco elementos, use desempacotamento estendido para separar o primeiro, os intermediários e o último.
3. Explique com código por que uma tupla com uma lista interna ainda pode ter o conteúdo da lista modificado.

## 3.20 — Fixando o conhecimento

1. Qual é a principal diferença entre lista e tupla?
2. O que significa dizer que uma tupla é imutável?
3. O que `lista[::-1]` faz?
4. Para que serve o operador `*` no desempacotamento?

---

# Capítulo 4 — Conjuntos

Conjuntos, ou sets, são coleções não ordenadas que não permitem elementos duplicados. Eles são muito úteis para remover repetição, verificar pertencimento e realizar operações matemáticas entre grupos de dados.

Ao final deste capítulo, você será capaz de:

- criar conjuntos;
- entender por que conjuntos não possuem elementos repetidos;
- adicionar e remover elementos;
- usar operações como união, interseção e diferença;
- decidir quando um conjunto é melhor que uma lista.

## 4.1 — O problema

Imagine que você recebeu uma lista de IDs de usuários que acessaram uma API. Alguns usuários aparecem mais de uma vez, porque fizeram várias requisições. Agora você precisa saber apenas quais usuários únicos acessaram o sistema.

Uma lista preserva repetição. Um conjunto elimina duplicidades automaticamente.

```python
ids = [10, 20, 10, 30, 20, 40]
ids_unicos = set(ids)

print(ids_unicos)
```

Saída possível:

```text
{40, 10, 20, 30}
```

A ordem pode variar, mas os valores duplicados foram removidos.

## 4.2 — O que é um conjunto?

Um conjunto é uma coleção sem valores repetidos.

```python
conjunto1 = {"a", "b", "c"}
conjunto2 = set([3, 4, 5, 6])

print(conjunto1)
print(conjunto2)
```

Conjuntos são úteis quando a existência do elemento importa mais do que sua posição.

## 4.3 — Criando um conjunto vazio

Para criar um conjunto vazio, use `set()`.

```python
conjunto_vazio = set()

print(type(conjunto_vazio))
```

Saída esperada:

```text
<class 'set'>
```

Não use `{}` para criar conjunto vazio, porque `{}` cria um dicionário vazio.

```python
valor = {}

print(type(valor))
```

Saída esperada:

```text
<class 'dict'>
```

## 4.4 — Verificando existência com `in` e `not in`

```python
conjunto = {1, 2, 3, 4, 5}

print(3 in conjunto)
print(6 not in conjunto)
```

Saída esperada:

```text
True
True
```

Esse tipo de verificação costuma ser um dos principais motivos para usar conjuntos. Quando você precisa verificar existência muitas vezes, conjuntos geralmente são mais adequados do que listas.

## 4.5 — Métodos de manipulação

Vamos usar este conjunto como base:

```python
conjunto = {1, 2, 3}
```

### 4.5.1 — `.add(elemento)`

Adiciona um único elemento.

```python
conjunto = {1, 2, 3}
conjunto.add(4)

print(conjunto)
```

Saída esperada:

```text
{1, 2, 3, 4}
```

Se o elemento já existir, o conjunto continua igual.

### 4.5.2 — `.update(iterável)`

Adiciona vários elementos de um iterável.

```python
conjunto = {1, 2, 3}
conjunto.update([3, 4, 5, 6])

print(conjunto)
```

Saída esperada:

```text
{1, 2, 3, 4, 5, 6}
```

O valor `3` não foi duplicado, porque conjuntos não aceitam repetição.

### 4.5.3 — `.remove(elemento)`

Remove um elemento. Se ele não existir, gera `KeyError`.

```python
conjunto = {1, 2, 3}
conjunto.remove(2)

print(conjunto)
```

Saída esperada:

```text
{1, 3}
```

### 4.5.4 — `.discard(elemento)`

Remove um elemento se ele existir. Se não existir, não gera erro.

```python
conjunto = {1, 2, 3}
conjunto.discard(99)

print(conjunto)
```

Saída esperada:

```text
{1, 2, 3}
```

Use `discard()` quando não tiver certeza se o elemento está no conjunto e não quiser tratar exceção.

### 4.5.5 — `.pop()`

Remove e retorna um elemento arbitrário.

```python
conjunto = {1, 2, 3}
removido = conjunto.pop()

print(removido)
print(conjunto)
```

A saída pode variar, porque conjuntos não garantem uma posição específica para remoção.

### 4.5.6 — `.clear()`

Remove todos os elementos.

```python
conjunto = {1, 2, 3}
conjunto.clear()

print(conjunto)
```

Saída esperada:

```text
set()
```

## 4.6 — Operações entre conjuntos

Vamos usar dois conjuntos:

```python
a = {1, 2, 3}
b = {3, 4, 5}
```

### 4.6.1 — União

A união retorna todos os elementos presentes em pelo menos um dos conjuntos.

```python
a = {1, 2, 3}
b = {3, 4, 5}

print(a.union(b))
print(a | b)
```

Saída esperada:

```text
{1, 2, 3, 4, 5}
{1, 2, 3, 4, 5}
```

### 4.6.2 — Interseção

A interseção retorna apenas os elementos comuns aos dois conjuntos.

```python
a = {1, 2, 3}
b = {3, 4, 5}

print(a.intersection(b))
print(a & b)
```

Saída esperada:

```text
{3}
{3}
```

### 4.6.3 — Diferença

A diferença retorna os elementos que estão no primeiro conjunto, mas não estão no segundo.

```python
a = {1, 2, 3}
b = {3, 4, 5}

print(a.difference(b))
print(a - b)
```

Saída esperada:

```text
{1, 2}
{1, 2}
```

A ordem importa: `a - b` é diferente de `b - a`.

```python
print(b - a)
```

Saída esperada:

```text
{4, 5}
```

### 4.6.4 — Diferença simétrica

A diferença simétrica retorna os elementos que estão em um dos conjuntos, mas não em ambos.

```python
a = {1, 2, 3}
b = {3, 4, 5}

print(a.symmetric_difference(b))
print(a ^ b)
```

Saída esperada:

```text
{1, 2, 4, 5}
{1, 2, 4, 5}
```

## 4.7 — Relacionamento entre conjuntos

Vamos usar estes conjuntos:

```python
a = {1, 2}
b = {1, 2, 3}
c = {4, 5}
```

### 4.7.1 — `.issubset()`

Verifica se um conjunto é subconjunto de outro.

```python
print(a.issubset(b))
print(a <= b)
```

Saída esperada:

```text
True
True
```

Todo elemento de `a` também está em `b`, então `a` é subconjunto de `b`.

### 4.7.2 — `.issuperset()`

Verifica se um conjunto contém todos os elementos de outro.

```python
print(b.issuperset(a))
print(b >= a)
```

Saída esperada:

```text
True
True
```

`b` é superconjunto de `a` porque contém todos os elementos de `a`.

### 4.7.3 — `.isdisjoint()`

Verifica se dois conjuntos não possuem elementos em comum.

```python
print(a.isdisjoint(c))
```

Saída esperada:

```text
True
```

Como `a` e `c` não compartilham nenhum valor, eles são disjuntos.

### 4.7.4 — `len()` e `.copy()`

```python
a = {1, 2}

print(len(a))

copia = a.copy()
print(copia)
```

Saída esperada:

```text
2
{1, 2}
```

`len()` retorna a quantidade de elementos. `copy()` cria uma cópia rasa do conjunto.

## 4.8 — O que pode dar errado?

### Criar conjunto vazio com `{}`

```python
conjunto = {}

print(type(conjunto))
```

Saída:

```text
<class 'dict'>
```

Para conjunto vazio, use `set()`.

### Esperar que o conjunto preserve ordem

```python
valores = {3, 1, 2}
print(valores)
```

A ordem de exibição não deve ser usada como regra de negócio. Se a ordem importa, use lista ou tupla.

### Usar `remove()` sem verificar se o elemento existe

```python
conjunto = {1, 2, 3}
conjunto.remove(99)
```

Esse código gera `KeyError`. Use `discard()` quando quiser remover sem erro caso o elemento não exista.

## 4.9 — Um pouco mais

Conjuntos são muito úteis para comparar permissões, tags ou identificadores.

```python
permissoes_usuario = {"ler", "criar", "editar"}
permissoes_necessarias = {"editar", "excluir"}

faltantes = permissoes_necessarias - permissoes_usuario

print(faltantes)
```

Saída esperada:

```text
{'excluir'}
```

Esse exemplo mostra quais permissões ainda faltam para o usuário.

## 4.10 — Resumo do capítulo

Conjuntos são coleções sem repetição e sem foco em ordem. Eles são ótimos para remover duplicidades, testar pertencimento e comparar grupos de dados usando operações como união, interseção, diferença e diferença simétrica.

## 4.11 — Exercícios

1. Crie uma lista com valores repetidos e converta para conjunto.
2. Crie dois conjuntos e calcule a união.
3. Calcule a interseção entre dois conjuntos.
4. Remova um item usando `discard()`.
5. Verifique se um conjunto é subconjunto de outro.

## 4.12 — Desafios

1. Dadas duas listas de IDs, descubra quais IDs aparecem nas duas listas.
2. Dadas duas listas de permissões, descubra quais permissões faltam para um usuário.
3. Crie uma função que receba uma lista e retorne outra lista sem valores duplicados.

## 4.13 — Fixando o conhecimento

1. Por que conjuntos não são indicados quando a ordem importa?
2. Qual é a diferença entre `remove()` e `discard()`?
3. O que a operação `a & b` retorna?
4. O que a operação `a - b` retorna?

---

# Capítulo 5 — Dicionários

Dicionários são estruturas de pares chave-valor. Eles são fundamentais em Python e aparecem em muitos contextos, como configuração de sistemas, payloads JSON, respostas de APIs, cache em memória e modelagem simples de objetos.

Ao final deste capítulo, você será capaz de:

- criar dicionários;
- acessar, modificar e remover valores;
- entender o que são chaves hashable;
- usar métodos como `get()`, `pop()`, `keys()`, `values()`, `items()`, `update()` e `setdefault()`;
- decidir quando usar dicionários em vez de listas.

## 5.1 — O problema

Imagine que você precisa representar uma pessoa no sistema.

Com uma lista, você poderia fazer:

```python
pessoa = ["Ana", 25, "São Paulo"]
```

Mas esse formato exige lembrar que:

- índice `0` é o nome;
- índice `1` é a idade;
- índice `2` é a cidade.

Isso funciona, mas não é tão legível. Com dicionário, cada valor possui uma chave descritiva:

```python
pessoa = {
    "nome": "Ana",
    "idade": 25,
    "cidade": "São Paulo",
}
```

Agora o código comunica melhor o significado dos dados.

## 5.2 — O que é um dicionário?

Um dicionário é uma coleção de pares chave-valor.

```python
dic1 = {"nome": "Ana", "nota": 10.0}
```

Também é possível criar com `dict()`:

```python
dic2 = dict(nome="Eva", nota=9.4)
```

Cada chave deve ser única. Se você repetir uma chave, o último valor sobrescreve o anterior.

```python
dados = {"nome": "Ana", "nome": "Eva"}

print(dados)
```

Saída esperada:

```text
{'nome': 'Eva'}
```

## 5.3 — Criando um dicionário vazio

```python
dicionario_vazio1 = {}
dicionario_vazio2 = dict()

print(dicionario_vazio1)
print(dicionario_vazio2)
```

Saída esperada:

```text
{}
{}
```

Diferente dos conjuntos, `{}` representa um dicionário vazio.

## 5.4 — Criando dicionário com lista de tuplas

É possível criar um dicionário a partir de uma lista de pares.

```python
lista_de_tuplas = [("nome", "Ana"), ("nota", 10.0)]

dicionario = dict(lista_de_tuplas)

print(dicionario)
```

Saída esperada:

```text
{'nome': 'Ana', 'nota': 10.0}
```

Cada tupla possui dois elementos: a chave e o valor.

## 5.5 — Tipos permitidos para chaves

As chaves de um dicionário precisam ser hashable. Em termos práticos, isso significa que o Python precisa conseguir calcular um valor de hash estável para localizar a chave com eficiência.

Tipos imutáveis como `str`, `int`, `float` e algumas `tuple` podem ser usados como chave. Tipos mutáveis como `list`, `dict` e `set` não podem.

### 5.5.1 — Chaves válidas

```python
dicionario_valido = {
    "nome": "Ana",
    1: "um",
    (2, 3): "par ordenado",
}

print(dicionario_valido["nome"])
print(dicionario_valido[(2, 3)])
```

Saída esperada:

```text
Ana
par ordenado
```

Nesse exemplo, as chaves são hashable.

### 5.5.2 — Chaves inválidas

```python
dicionario_invalido = {[1, 2]: "lista"}
```

Esse código gera erro:

```text
TypeError: unhashable type: 'list'
```

Listas são mutáveis, então não podem ser usadas como chave.

### 5.5.3 — Atenção com tuplas contendo objetos mutáveis

Uma tupla pode ser usada como chave apenas se todos os seus elementos também forem hashable.

```python
hash((1, 2, [3, 4]))
```

Esse código gera erro, porque a tupla contém uma lista.

## 5.6 — Tipos permitidos para valores

Os valores de um dicionário podem ser de qualquer tipo: números, strings, listas, tuplas, outros dicionários, objetos e até funções.

```python
dicionario = {
    "nome": "João",
    "idade": 25,
    "notas": [10.0, 6.3, 7.5],
    "endereco": {
        "cidade": "São Paulo",
        "bairro": "Centro",
    },
}
```

Esse tipo de estrutura é muito comum em APIs, especialmente quando os dados são convertidos para JSON.

## 5.7 — Acessando e modificando dados

Vamos usar este dicionário:

```python
dic = {"nome": "Ana", "nota": 10.0}
```

### 5.7.1 — Acessar valor por chave

```python
print(dic["nome"])
```

Saída esperada:

```text
Ana
```

### 5.7.2 — Modificar valor

```python
dic["nota"] = 9.2

print(dic)
```

Saída esperada:

```text
{'nome': 'Ana', 'nota': 9.2}
```

### 5.7.3 — Adicionar nova chave

```python
dic["status"] = "aprovada"

print(dic)
```

Saída esperada:

```text
{'nome': 'Ana', 'nota': 9.2, 'status': 'aprovada'}
```

Se a chave não existe, o Python cria. Se a chave já existe, o Python atualiza o valor.

### 5.7.4 — Remover com `del`

```python
del dic["nome"]

print(dic)
```

Saída esperada:

```text
{'nota': 9.2, 'status': 'aprovada'}
```

Se a chave não existir, `del` gera `KeyError`.

## 5.8 — Verificando existência de chave

Use `in` para verificar se uma chave existe.

```python
dic = {"nome": "Ana", "nota": 10.0}

print("nome" in dic)
print("idade" not in dic)
```

Saída esperada:

```text
True
True
```

Atenção: por padrão, `in` verifica chaves, não valores.

```python
dic = {"nome": "Ana", "nota": 10.0}

print("Ana" in dic)
```

Saída esperada:

```text
False
```

`"Ana"` é valor, não chave.

## 5.9 — Métodos de visualização

Vamos usar:

```python
dicionario = {"nome": "João", "idade": 28}
```

### 5.9.1 — `.keys()`

Retorna uma visão das chaves.

```python
print(dicionario.keys())
```

Saída esperada:

```text
dict_keys(['nome', 'idade'])
```

### 5.9.2 — `.values()`

Retorna uma visão dos valores.

```python
print(dicionario.values())
```

Saída esperada:

```text
dict_values(['João', 28])
```

### 5.9.3 — `.items()`

Retorna uma visão dos pares chave-valor.

```python
print(dicionario.items())
```

Saída esperada:

```text
dict_items([('nome', 'João'), ('idade', 28)])
```

Esse método é muito usado em loops:

```python
for chave, valor in dicionario.items():
    print(chave, valor)
```

Saída esperada:

```text
nome João
idade 28
```

### 5.9.4 — `len(dicionario)`

Retorna a quantidade de pares chave-valor.

```python
print(len(dicionario))
```

Saída esperada:

```text
2
```

## 5.10 — Métodos de busca e remoção

### 5.10.1 — `.get(chave, padrão=None)`

O método `get()` busca uma chave sem gerar erro caso ela não exista.

```python
dicionario = {"nome": "João", "idade": 28}

print(dicionario.get("nome"))
print(dicionario.get("altura", "N/A"))
```

Saída esperada:

```text
João
N/A
```

Use `get()` quando uma chave pode não existir.

### 5.10.2 — `.pop(chave, padrão)`

Remove uma chave e retorna seu valor.

```python
dicionario = {"nome": "João", "idade": 28}

idade = dicionario.pop("idade")

print(idade)
print(dicionario)
```

Saída esperada:

```text
28
{'nome': 'João'}
```

Com valor padrão:

```python
dicionario = {"nome": "João"}

altura = dicionario.pop("altura", "N/A")

print(altura)
print(dicionario)
```

Saída esperada:

```text
N/A
{'nome': 'João'}
```

Se a chave não existir e você não informar padrão, `pop()` gera `KeyError`.

### 5.10.3 — `list(dicionario)`

Converter um dicionário para lista retorna uma lista com as chaves.

```python
dicionario = {"nome": "João", "idade": 28}

chaves = list(dicionario)

print(chaves)
```

Saída esperada:

```text
['nome', 'idade']
```

### 5.10.4 — `.clear()`

Remove todos os pares chave-valor.

```python
dicionario = {"nome": "João", "idade": 28}

dicionario.clear()

print(dicionario)
```

Saída esperada:

```text
{}
```

### 5.10.5 — `.setdefault(chave, padrão=None)`

O método `setdefault()` retorna o valor de uma chave. Se a chave não existir, ele cria a chave com o valor padrão e retorna esse valor.

```python
dicionario = {"nome": "João", "idade": 28}

altura = dicionario.setdefault("altura", "N/A")

print(altura)
print(dicionario)
```

Saída esperada:

```text
N/A
{'nome': 'João', 'idade': 28, 'altura': 'N/A'}
```

Esse método pode ser útil para inicializar estruturas agrupadas.

```python
usuarios_por_cidade = {}

cidade = "São Paulo"
usuario = "Ana"

usuarios_por_cidade.setdefault(cidade, []).append(usuario)

print(usuarios_por_cidade)
```

Saída esperada:

```text
{'São Paulo': ['Ana']}
```

### 5.10.6 — `dict.fromkeys(iterável_de_chaves, valor=None)`

Cria um novo dicionário com as chaves informadas e o mesmo valor padrão para todas.

```python
campos = ["nome", "idade"]

dados = dict.fromkeys(campos, "N/A")

print(dados)
```

Saída esperada:

```text
{'nome': 'N/A', 'idade': 'N/A'}
```

Cuidado ao usar valores mutáveis com `fromkeys()`:

```python
dados = dict.fromkeys(["a", "b"], [])

dados["a"].append(1)

print(dados)
```

Saída esperada:

```text
{'a': [1], 'b': [1]}
```

As duas chaves compartilham a mesma lista. Para evitar isso, crie as listas separadamente, por exemplo com dict comprehension:

```python
dados = {chave: [] for chave in ["a", "b"]}

dados["a"].append(1)

print(dados)
```

Saída esperada:

```text
{'a': [1], 'b': []}
```

## 5.11 — Outros métodos de manipulação

Vamos usar:

```python
dicionario = {"a": 1, "b": 2, "c": 3}
```

### 5.11.1 — `.popitem()`

Remove e retorna o último par inserido.

```python
dicionario = {"a": 1, "b": 2, "c": 3}

item = dicionario.popitem()

print(item)
print(dicionario)
```

Saída esperada:

```text
('c', 3)
{'a': 1, 'b': 2}
```

### 5.11.2 — `reversed(dicionario)`

Retorna um iterador reverso sobre as chaves.

```python
dicionario = {"a": 1, "b": 2, "c": 3}

print(list(reversed(dicionario)))
```

Saída esperada:

```text
['c', 'b', 'a']
```

### 5.11.3 — `.update(outro)`

Atualiza um dicionário com pares chave-valor de outro.

```python
dicionario = {"a": 1, "b": 2, "c": 3}

dicionario.update({"c": 4, "d": 5})

print(dicionario)
```

Saída esperada:

```text
{'a': 1, 'b': 2, 'c': 4, 'd': 5}
```

A chave `"c"` já existia, então seu valor foi atualizado. A chave `"d"` não existia, então foi criada.

### 5.11.4 — `.copy()`

Cria uma cópia rasa do dicionário.

```python
dicionario = {"a": 1, "b": 2, "c": 3}

copia = dicionario.copy()

print(copia)
```

Saída esperada:

```text
{'a': 1, 'b': 2, 'c': 3}
```

Assim como em listas, a cópia é rasa. Se os valores forem objetos mutáveis, eles continuam compartilhados.

## 5.12 — Dict comprehension

Assim como listas, dicionários também podem ser criados por compreensão.

```python
numeros = [1, 2, 3, 4]
quadrados = {numero: numero ** 2 for numero in numeros}

print(quadrados)
```

Saída esperada:

```text
{1: 1, 2: 4, 3: 9, 4: 16}
```

Esse recurso é útil quando queremos criar um mapeamento a partir de uma sequência.

Outro exemplo:

```python
usuarios = [
    {"id": 1, "nome": "Ana"},
    {"id": 2, "nome": "Bruno"},
]

usuarios_por_id = {usuario["id"]: usuario for usuario in usuarios}

print(usuarios_por_id[1])
```

Saída esperada:

```text
{'id': 1, 'nome': 'Ana'}
```

Esse padrão é comum quando precisamos transformar uma lista em um índice para buscas rápidas por ID.

## 5.13 — O que pode dar errado?

### Acessar chave inexistente com colchetes

```python
dados = {"nome": "Ana"}

print(dados["idade"])
```

Esse código gera `KeyError`. Use `get()` quando a chave pode não existir.

### Usar lista como chave

```python
dados = {[1, 2]: "valor"}
```

Esse código gera `TypeError`, porque listas não são hashable.

### Achar que `in` procura nos valores

```python
dados = {"nome": "Ana"}

print("Ana" in dados)
```

Saída:

```text
False
```

O operador `in` procura nas chaves. Para procurar nos valores:

```python
print("Ana" in dados.values())
```

## 5.14 — Um pouco mais

Dicionários são uma das estruturas mais importantes para quem desenvolve backend. Em APIs, é comum manipular dados em formatos parecidos com dicionários.

Exemplo de payload de usuário:

```python
usuario = {
    "id": 10,
    "nome": "Ana",
    "email": "ana@example.com",
    "ativo": True,
}
```

Podemos validar campos obrigatórios com conjuntos:

```python
campos_obrigatorios = {"nome", "email"}
campos_recebidos = set(usuario.keys())

faltando = campos_obrigatorios - campos_recebidos

if faltando:
    print(f"Campos faltando: {faltando}")
else:
    print("Payload válido")
```

Saída esperada:

```text
Payload válido
```

Esse exemplo mostra como dicionários e conjuntos podem trabalhar juntos.

## 5.15 — Resumo do capítulo

Dicionários armazenam pares chave-valor. Eles são ideais quando precisamos buscar dados por uma chave significativa, como `id`, `email`, `nome` ou `status`. As chaves precisam ser hashable, enquanto os valores podem ser de qualquer tipo.

## 5.16 — Exercícios

1. Crie um dicionário representando um usuário com nome, idade e email.
2. Acesse o email usando a chave correspondente.
3. Atualize a idade do usuário.
4. Adicione uma chave `ativo` com valor `True`.
5. Use `get()` para acessar uma chave que talvez não exista.
6. Use `items()` para percorrer todas as chaves e valores.

## 5.17 — Desafios

1. Crie uma lista de dicionários representando produtos e filtre apenas os produtos com preço maior que `10`.
2. Transforme uma lista de usuários em um dicionário indexado pelo `id`.
3. Crie uma função que recebe um dicionário de usuário e verifica se os campos obrigatórios `nome` e `email` existem.
4. Mostre com código o problema de usar `dict.fromkeys()` com uma lista vazia como valor padrão.

## 5.18 — Fixando o conhecimento

1. Qual é a diferença entre acessar `dados["chave"]` e `dados.get("chave")`?
2. Por que listas não podem ser chaves de dicionários?
3. O que `items()` retorna?
4. O que acontece quando `update()` recebe uma chave que já existe?

---

# Capítulo 6 — Escolhendo a estrutura correta

Agora que já vimos listas, tuplas, conjuntos e dicionários, o próximo passo é saber escolher a estrutura adequada para cada situação.

Ao final deste capítulo, você será capaz de:

- comparar as principais estruturas nativas do Python;
- escolher uma estrutura com base no problema;
- entender impactos básicos de performance;
- aplicar boas práticas em cenários reais.

## 6.1 — O problema

Uma mesma informação pode ser representada de várias formas. Por exemplo, os dados de um produto podem ser guardados em uma lista, tupla ou dicionário.

```python
produto_lista = ["Arroz", 5.99, 30]
produto_tupla = ("Arroz", 5.99, 30)
produto_dict = {"nome": "Arroz", "preco": 5.99, "quantidade": 30}
```

As três formas funcionam, mas cada uma comunica uma intenção diferente.

- A lista sugere uma coleção mutável e ordenada.
- A tupla sugere um conjunto fixo de valores.
- O dicionário sugere dados nomeados por chave.

Em código profissional, clareza costuma ser mais importante do que economizar algumas letras.

## 6.2 — Comparação geral

| Situação | Estrutura recomendada | Motivo |
|---|---|---|
| Preciso manter uma sequência editável | Lista | É ordenada e mutável |
| Preciso representar um grupo fixo de valores | Tupla | É ordenada e imutável |
| Preciso remover duplicados | Conjunto | Não permite repetição |
| Preciso testar pertencimento muitas vezes | Conjunto | Busca por item costuma ser eficiente |
| Preciso mapear uma chave para um valor | Dicionário | Acesso direto por chave |
| Preciso representar um objeto simples | Dicionário | Chaves deixam os campos explícitos |
| Preciso retornar múltiplos valores de uma função | Tupla | É simples e idiomática |

## 6.3 — Complexidade básica

Complexidade é uma forma de estimar como o custo de uma operação cresce conforme a quantidade de dados aumenta.

| Operação | Lista | Tupla | Conjunto | Dicionário |
|---|---:|---:|---:|---:|
| Acesso por índice | O(1) | O(1) | Não se aplica | Não se aplica |
| Busca por valor com `in` | O(n) | O(n) | O(1) médio | O(1) médio para chaves |
| Inserir no final | O(1) amortizado | Não se aplica | O(1) médio | O(1) médio |
| Inserir no começo/meio | O(n) | Não se aplica | Não depende de posição | Não depende de posição |
| Remover por valor | O(n) | Não se aplica | O(1) médio | O(1) médio por chave |

Esses valores são uma visão prática para CPython, que é a implementação mais usada do Python. Na maioria dos programas pequenos, legibilidade vem primeiro. Em coleções grandes ou operações repetidas muitas vezes, a escolha da estrutura passa a impactar bastante.

## 6.4 — Exemplo prático: validando campos de uma API

Imagine que uma API recebe o seguinte payload:

```python
payload = {
    "nome": "Ana",
    "email": "ana@example.com",
    "idade": 25,
}
```

Queremos verificar se os campos obrigatórios estão presentes.

```python
campos_obrigatorios = {"nome", "email"}
campos_recebidos = set(payload.keys())

faltando = campos_obrigatorios - campos_recebidos

if faltando:
    print(f"Campos obrigatórios ausentes: {faltando}")
else:
    print("Payload válido")
```

Saída esperada:

```text
Payload válido
```

Nesse exemplo, usamos:

- dicionário para representar o payload;
- conjunto para representar os campos obrigatórios;
- diferença de conjuntos para descobrir o que está faltando.

## 6.5 — Exemplo prático: indexando usuários por ID

Buscar um usuário em uma lista exige percorrer os itens até encontrar o ID desejado.

```python
usuarios = [
    {"id": 1, "nome": "Ana"},
    {"id": 2, "nome": "Bruno"},
    {"id": 3, "nome": "Carla"},
]

usuario_encontrado = None

for usuario in usuarios:
    if usuario["id"] == 2:
        usuario_encontrado = usuario
        break

print(usuario_encontrado)
```

Saída esperada:

```text
{'id': 2, 'nome': 'Bruno'}
```

Se essa busca acontecer muitas vezes, podemos criar um dicionário indexado por ID.

```python
usuarios_por_id = {usuario["id"]: usuario for usuario in usuarios}

print(usuarios_por_id[2])
```

Saída esperada:

```text
{'id': 2, 'nome': 'Bruno'}
```

O dicionário deixa a busca mais direta e o código mais claro.

## 6.6 — Boas práticas

### Prefira nomes claros

```python
# Pouco claro
x = ["Ana", "Bruno"]

# Melhor
nomes_usuarios = ["Ana", "Bruno"]
```

### Evite listas com muitos significados por índice

```python
produto = ["Arroz", 5.99, 30]
```

Esse código funciona, mas exige lembrar o significado de cada posição. Um dicionário pode ser mais claro:

```python
produto = {
    "nome": "Arroz",
    "preco": 5.99,
    "quantidade": 30,
}
```

### Use conjuntos para remover duplicidade

```python
emails = ["a@email.com", "b@email.com", "a@email.com"]
emails_unicos = set(emails)

print(emails_unicos)
```

### Use tuplas para retornos simples

```python
def dividir(a, b):
    quociente = a // b
    resto = a % b
    return quociente, resto

q, r = dividir(10, 3)

print(q)
print(r)
```

Saída esperada:

```text
3
1
```

## 6.7 — O que pode dar errado?

### Usar lista quando precisa de busca rápida por chave

Se você busca usuários pelo ID muitas vezes, uma lista pode deixar o código repetitivo e menos eficiente. Um dicionário indexado por ID costuma ser melhor.

### Usar conjunto quando precisa preservar duplicados

Conjuntos removem duplicatas. Se a quantidade de ocorrências importa, não use conjunto como estrutura principal.

```python
valores = [1, 1, 2, 3]
print(set(valores))
```

Saída possível:

```text
{1, 2, 3}
```

O segundo `1` desaparece.

### Usar tupla achando que tudo dentro dela é impossível de mudar

Tuplas são imutáveis na estrutura externa, mas podem conter objetos mutáveis. Se isso for um problema, evite colocar listas ou dicionários dentro de tuplas.

## 6.8 — Resumo do capítulo

A escolha da estrutura deve seguir a necessidade do problema. Listas são boas para sequências mutáveis, tuplas para grupos fixos, conjuntos para valores únicos e dicionários para mapeamento chave-valor. Bons programas não usam estruturas apenas porque funcionam, mas porque comunicam intenção.

## 6.9 — Exercícios

1. Escolha a estrutura ideal para guardar uma lista de tarefas editável.
2. Escolha a estrutura ideal para representar coordenadas `(x, y)`.
3. Escolha a estrutura ideal para remover emails duplicados.
4. Escolha a estrutura ideal para buscar produtos por código.
5. Transforme uma lista de usuários em um dicionário indexado por ID.

## 6.10 — Desafios

1. Crie uma função que recebe uma lista de produtos e retorna apenas os nomes dos produtos em estoque.
2. Crie uma função que recebe dois conjuntos de permissões e retorna quais permissões faltam.
3. Crie uma função que valida se um dicionário possui um conjunto mínimo de chaves obrigatórias.
4. Crie um pequeno cadastro de usuários usando uma lista de dicionários e depois gere um índice por email.

## 6.11 — Fixando o conhecimento

1. Quando usar lista em vez de conjunto?
2. Quando usar dicionário em vez de lista?
3. Por que tuplas comunicam a ideia de dados fixos?
4. Qual estrutura você usaria para armazenar tags únicas de um artigo?

---

# Referências bibliográficas

PYTHON SOFTWARE FOUNDATION. **5. Data Structures**. Python 3 Documentation. Disponível em: <https://docs.python.org/3/tutorial/datastructures.html>. Acesso em: 31 maio 2026.

PYTHON SOFTWARE FOUNDATION. **Built-in Types**. Python 3 Standard Library. Disponível em: <https://docs.python.org/3/library/stdtypes.html>. Acesso em: 31 maio 2026.

PYTHON SOFTWARE FOUNDATION. **copy — Shallow and deep copy operations**. Python 3 Standard Library. Disponível em: <https://docs.python.org/3/library/copy.html>. Acesso em: 31 maio 2026.

PYTHON SOFTWARE FOUNDATION. **Data model**. Python 3 Language Reference. Disponível em: <https://docs.python.org/3/reference/datamodel.html>. Acesso em: 31 maio 2026.

PYTHON SOFTWARE FOUNDATION. **TimeComplexity**. Python Wiki. Disponível em: <https://wiki.python.org/moin/TimeComplexity>. Acesso em: 31 maio 2026.
