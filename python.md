# Apostila Didática de Python

> Material reorganizado e ampliado a partir das anotações originais sobre Python, com foco em clareza, exemplos pequenos e explicações diretas.

---

## Sobre esta apostila

Esta apostila apresenta os fundamentos da linguagem Python de maneira progressiva. A ideia é começar pelos conceitos essenciais, como variáveis, tipos de dados, entrada e saída, e avançar até estruturas de controle, funções, built-in functions, expressões regulares, comprehensions e manipulação de JSON.

O objetivo não é decorar todos os detalhes da linguagem, mas entender como os recursos funcionam, quando usá-los e quais erros comuns evitar. Cada tópico traz explicações curtas, exemplos pequenos e comentários sobre o que está acontecendo no código.

---

## Como estudar por esta apostila

Leia os capítulos na ordem, principalmente se você ainda está consolidando a base da linguagem. Sempre que encontrar um bloco de código, copie, execute e altere pequenos trechos para observar o comportamento do Python.

Não pule os exercícios. Em programação, entender a explicação é importante, mas a fixação acontece quando você escreve código, erra, lê a mensagem de erro e corrige.

---

## Índice

1. [Capítulo 1 — Visão geral da linguagem Python](#capítulo-1--visão-geral-da-linguagem-python)
2. [Capítulo 2 — Sintaxe básica, entrada, saída e variáveis](#capítulo-2--sintaxe-básica-entrada-saída-e-variáveis)
3. [Capítulo 3 — Tipos de dados e conversão de tipos](#capítulo-3--tipos-de-dados-e-conversão-de-tipos)
4. [Capítulo 4 — Strings em Python](#capítulo-4--strings-em-python)
5. [Capítulo 5 — Condicionais e operadores](#capítulo-5--condicionais-e-operadores)
6. [Capítulo 6 — Estrutura condicional `match/case`](#capítulo-6--estrutura-condicional-matchcase)
7. [Capítulo 7 — Laços de repetição](#capítulo-7--laços-de-repetição)
8. [Capítulo 8 — Funções em Python](#capítulo-8--funções-em-python)
9. [Capítulo 9 — Built-in functions em Python](#capítulo-9--built-in-functions-em-python)
10. [Capítulo 10 — Expressões regulares](#capítulo-10--expressões-regulares)
11. [Capítulo 11 — Dict comprehension](#capítulo-11--dict-comprehension)
12. [Capítulo 12 — JSON em Python e uso com APIs](#capítulo-12--json-em-python-e-uso-com-apis)
13. [Referências bibliográficas](#referências-bibliográficas)

---

# Capítulo 1 — Visão geral da linguagem Python

Python é uma linguagem de programação de alto nível, conhecida por sua sintaxe simples, legível e próxima da linguagem humana. Ela é muito usada em desenvolvimento web, automação, análise de dados, inteligência artificial, scripts, APIs, testes e diversas outras áreas.

Ao final deste capítulo, você será capaz de:

- entender o que caracteriza a linguagem Python;
- diferenciar código interpretado de código compilado de forma simplificada;
- reconhecer por que a indentação é importante em Python;
- entender a importância de escrever código legível.

## 1.1 — O problema

Quando uma pessoa começa a programar, uma das maiores dificuldades é entender a sintaxe da linguagem. Em algumas linguagens, é comum encontrar muitos símbolos, chaves e estruturas rígidas logo no início.

Python tenta reduzir essa barreira. Em vez de exigir muito código para tarefas simples, a linguagem privilegia clareza e leitura. Isso não significa que Python seja uma linguagem limitada; significa que ela busca deixar o código mais direto.

## 1.2 — O que é Python?

Python é uma linguagem de programação de propósito geral. Isso significa que ela não foi criada para resolver apenas um tipo específico de problema. Com Python, é possível escrever desde pequenos scripts de automação até aplicações web, serviços backend, ferramentas de linha de comando, processamento de dados e sistemas mais complexos.

Uma característica importante é que Python possui tipagem dinâmica. Isso quer dizer que você não precisa declarar previamente o tipo de uma variável. O próprio interpretador identifica o tipo do valor em tempo de execução.

```python
idade = 25
nome = "João"
altura = 1.75
```

Nesse exemplo, `idade` recebe um número inteiro, `nome` recebe uma string e `altura` recebe um número decimal. Não foi necessário escrever `int idade`, `str nome` ou `float altura`, como acontece em outras linguagens.

## 1.3 — Indentação

Em Python, a indentação não é apenas estética. Ela define os blocos de código.

```python
idade = 18

if idade >= 18:
    print("Maior de idade")
```

A linha `print("Maior de idade")` está indentada, portanto pertence ao bloco do `if`. Se a indentação estiver errada, o Python pode gerar erro ou executar o código de maneira diferente do esperado.

## 1.4 — Código legível

Python incentiva a escrita de código simples e legível. Um código legível é aquele que outra pessoa consegue entender sem precisar adivinhar o que está acontecendo.

Compare:

```python
x = 10
if x > 7:
    print("ok")
```

Com:

```python
nota = 10
if nota > 7:
    print("Aluno aprovado")
```

Os dois códigos funcionam, mas o segundo deixa a intenção muito mais clara.

## 1.5 — Um pouco mais

Python é uma linguagem interpretada na prática do dia a dia: você escreve o código em arquivos `.py` e executa com o interpretador Python. Internamente, o Python também realiza etapas como compilação para bytecode, mas para quem está aprendendo o mais importante é entender que você não precisa compilar manualmente como em linguagens como C.

## 1.6 — Resumo do capítulo

Neste capítulo, vimos que Python é uma linguagem de alto nível, legível e de propósito geral. Também vimos que a indentação define blocos de código e que nomes claros ajudam muito na leitura e manutenção do programa.

## 1.7 — Exercícios

1. Crie três variáveis: `nome`, `idade` e `cidade`.
2. Imprima uma frase usando essas três variáveis.
3. Crie um `if` que verifica se a idade é maior ou igual a 18.
4. Altere a indentação de propósito e observe o erro gerado pelo Python.

## 1.8 — Desafio

Escreva um pequeno programa que receba o nome de uma pessoa, a idade e diga se ela pode tirar carteira de motorista.

---

# Capítulo 2 — Sintaxe básica, entrada, saída e variáveis

Neste capítulo, vamos estudar os primeiros elementos práticos da linguagem: como imprimir informações, como ler dados digitados pelo usuário e como armazenar valores em variáveis.

Ao final deste capítulo, você será capaz de:

- usar `print()` para exibir dados;
- usar `input()` para receber dados do usuário;
- criar variáveis;
- entender a diferença entre nome da variável e valor armazenado;
- usar `type()` para verificar tipos.

## 2.1 — O problema

Todo programa precisa lidar com dados. Às vezes, os dados já estão no código. Outras vezes, eles vêm do usuário, de um arquivo, de uma API ou de um banco de dados.

Antes de trabalhar com sistemas maiores, é essencial entender como armazenar e exibir informações simples.

## 2.2 — Saída de dados com `print()`

A função `print()` exibe informações no console.

```python
print("Olá, mundo!")
```

Saída:

```text
Olá, mundo!
```

Também podemos imprimir valores armazenados em variáveis:

```python
nome = "Maria"
print(nome)
```

Saída:

```text
Maria
```

## 2.3 — Entrada de dados com `input()`

A função `input()` permite receber um valor digitado pelo usuário.

```python
nome = input("Digite seu nome: ")
print("Olá,", nome)
```

O texto dentro de `input()` aparece como mensagem para o usuário. O valor digitado é armazenado na variável `nome`.

## 2.4 — Atenção: `input()` sempre retorna string

Mesmo que o usuário digite um número, o valor retornado por `input()` será uma string.

```python
idade = input("Digite sua idade: ")
print(type(idade))
```

Se o usuário digitar `25`, a saída será algo semelhante a:

```text
<class 'str'>
```

Para usar esse valor como número, é preciso converter:

```python
idade = int(input("Digite sua idade: "))
print(idade + 1)
```

## 2.5 — Variáveis

Uma variável é um nome que referencia um valor na memória.

```python
idade = 25
nome = "João"
altura = 1.75
```

Nesse exemplo:

- `idade` referencia o valor `25`;
- `nome` referencia o texto `"João"`;
- `altura` referencia o valor decimal `1.75`.

## 2.6 — Alocação de memória

Quando você cria uma variável, o Python cria ou reutiliza objetos na memória e faz o nome da variável apontar para esse objeto.

```python
a = 5
b = 5
```

Em alguns casos, especialmente com valores imutáveis pequenos, o Python pode reutilizar o mesmo objeto em memória. Porém, para quem está começando, o ponto principal é entender que a variável não é uma “caixa física”; ela é um nome associado a um objeto.

Você pode visualizar isso com `id()`:

```python
a = 5
b = 5

print(id(a))
print(id(b))
```

O `id()` retorna um identificador do objeto. Se os dois valores apontarem para o mesmo objeto, os identificadores serão iguais.

## 2.7 — Verificação de tipos com `type()`

A função `type()` mostra o tipo de um valor ou variável.

```python
numero = 10
texto = "Olá, mundo!"
lista = [1, 2, 3]

print(type(numero))
print(type(texto))
print(type(lista))
```

Saída esperada:

```text
<class 'int'>
<class 'str'>
<class 'list'>
```

Isso é útil para depurar código e entender que tipo de dado está sendo manipulado.

## 2.8 — O que pode dar errado?

Um erro comum é tentar fazer conta com um valor recebido por `input()` sem convertê-lo.

```python
idade = input("Digite sua idade: ")
print(idade + 1)
```

Esse código gera erro, porque `idade` é uma string e `1` é um inteiro.

Forma correta:

```python
idade = int(input("Digite sua idade: "))
print(idade + 1)
```

## 2.9 — Resumo do capítulo

Neste capítulo, vimos que `print()` exibe informações, `input()` recebe dados do usuário e variáveis armazenam referências para valores. Também vimos que `input()` sempre retorna string e que `type()` ajuda a verificar o tipo de um dado.

## 2.10 — Exercícios

1. Peça o nome do usuário e imprima uma mensagem de boas-vindas.
2. Peça dois números inteiros e imprima a soma.
3. Peça a idade do usuário e mostre a idade dele no próximo ano.
4. Use `type()` para verificar o tipo de três variáveis diferentes.

## 2.11 — Desafio

Crie um programa que receba nome, idade e altura. Depois, imprima uma frase formatada usando esses três valores.

---

# Capítulo 3 — Tipos de dados e conversão de tipos

Python possui vários tipos de dados. Neste capítulo, vamos focar nos tipos básicos e na conversão entre eles.

Ao final deste capítulo, você será capaz de:

- identificar tipos básicos em Python;
- converter valores usando `int()`, `float()`, `str()` e `bool()`;
- entender o conceito de truthy e falsy;
- evitar erros comuns de conversão.

## 3.1 — O problema

Nem todo dado chega ao programa no formato ideal. Uma idade pode chegar como texto, um preço pode vir como string e uma resposta do usuário pode precisar ser interpretada como verdadeiro ou falso.

Por isso, converter tipos corretamente é uma habilidade essencial.

## 3.2 — Tipos básicos

Os principais tipos básicos são:

| Tipo | Uso principal | Exemplo |
|---|---|---|
| `int` | números inteiros | `10` |
| `float` | números decimais | `3.14` |
| `str` | textos | `"Python"` |
| `bool` | valores lógicos | `True` ou `False` |

Exemplo:

```python
idade = 25
preco = 19.90
nome = "Ana"
ativo = True
```

## 3.3 — Conversão para inteiro com `int()`

A função `int()` converte um valor para inteiro.

```python
valor_float = 19.85
valor_str = "100"

inteiro_de_float = int(valor_float)
inteiro_de_str = int(valor_str)

print(inteiro_de_float)
print(inteiro_de_str)
```

Saída:

```text
19
100
```

Ao converter um `float` para `int`, o Python remove a parte decimal. Ele não arredonda.

## 3.4 — Conversão para decimal com `float()`

A função `float()` converte um valor para número de ponto flutuante.

```python
valor_int = 42
valor_str = "3.14"

print(float(valor_int))
print(float(valor_str))
```

Saída:

```text
42.0
3.14
```

## 3.5 — Conversão para string com `str()`

A função `str()` transforma um valor em texto.

```python
valor_int = 500
valor_float = 99.9
valor_lista = [1, 2, 3]

print(str(valor_int))
print(str(valor_float))
print(str(valor_lista))
```

Saída:

```text
500
99.9
[1, 2, 3]
```

## 3.6 — Conversão para booleano com `bool()`

A função `bool()` converte um valor para `True` ou `False`.

Valores considerados falsos em Python:

- `0`;
- `0.0`;
- `None`;
- string vazia `""`;
- listas vazias `[]`;
- tuplas vazias `()`;
- dicionários vazios `{}`;
- conjuntos vazios `set()`.

Exemplo:

```python
print(bool(0))
print(bool(""))
print(bool([]))
print(bool(1))
print(bool("texto"))
```

Saída:

```text
False
False
False
True
True
```

## 3.7 — Convertendo coleções

Também é possível converter entre alguns tipos de coleção.

```python
texto = "abc"
tupla = (1, 2, 3, 2)

lista_de_texto = list(texto)
conjunto_de_tupla = set(tupla)
tupla_de_lista = tuple(lista_de_texto)

print(lista_de_texto)
print(conjunto_de_tupla)
print(tupla_de_lista)
```

Saída possível:

```text
['a', 'b', 'c']
{1, 2, 3}
('a', 'b', 'c')
```

A conversão para `set` remove duplicados, porque conjuntos não armazenam elementos repetidos.

## 3.8 — O que pode dar errado?

Nem toda string pode ser convertida para número.

```python
valor = "Python"
numero = int(valor)
```

Esse código gera `ValueError`, pois o texto `"Python"` não representa um número inteiro.

Uma forma mais segura é validar antes ou tratar o erro:

```python
valor = input("Digite um número: ")

try:
    numero = int(valor)
    print(numero * 2)
except ValueError:
    print("Valor inválido. Digite apenas números inteiros.")
```

## 3.9 — Resumo do capítulo

Neste capítulo, vimos os tipos básicos `int`, `float`, `str` e `bool`. Também vimos que conversões explícitas são feitas com funções como `int()`, `float()`, `str()` e `bool()`, e que valores vazios geralmente são considerados `False`.

## 3.10 — Exercícios

1. Converta a string `"10"` para inteiro e some com `5`.
2. Converta o número `25` para string e concatene com uma frase.
3. Teste `bool()` com uma string vazia, uma lista vazia e um número diferente de zero.
4. Receba um preço com `input()` e converta para `float`.

## 3.11 — Desafio

Crie um programa que receba dois números via `input()`, converta os valores corretamente e exiba soma, subtração, multiplicação e divisão.

---

# Capítulo 4 — Strings em Python

Strings são sequências de caracteres usadas para representar texto. Em Python, elas podem ser escritas com aspas simples ou duplas.

Ao final deste capítulo, você será capaz de:

- criar strings;
- usar f-strings;
- acessar caracteres por índice;
- usar slicing;
- aplicar métodos comuns de string;
- verificar conteúdo com `in`, `startswith()` e `endswith()`.

## 4.1 — O problema

Grande parte dos dados manipulados em sistemas reais é texto: nomes, e-mails, mensagens, códigos, documentos, logs, respostas de APIs e entradas de usuários.

Por isso, dominar strings é essencial.

## 4.2 — Criando strings

```python
mensagem_simples = 'Olá, mundo!'
mensagem_dupla = "Python é incrível!"
```

Aspas simples e duplas funcionam da mesma forma. A escolha geralmente depende de legibilidade.

## 4.3 — Formatação com f-strings

As f-strings permitem inserir variáveis e expressões diretamente dentro de uma string.

```python
estudante = "Pedro"
nota = 10

mensagem = f"{estudante} tirou a nota {nota}!"
print(mensagem)
```

Saída:

```text
Pedro tirou a nota 10!
```

A letra `f` antes das aspas indica que a string será formatada. Tudo que estiver dentro de `{}` será avaliado pelo Python.

## 4.4 — Formatando números com f-strings

Também podemos controlar casas decimais.

```python
preco = 19.9
print(f"Preço: R$ {preco:.2f}")
```

Saída:

```text
Preço: R$ 19.90
```

O trecho `:.2f` indica que queremos exibir o número com duas casas decimais.

## 4.5 — Indexação

Strings são sequências. Cada caractere possui uma posição, chamada índice. A contagem começa em zero.

```python
texto = "Python"

print(texto[0])
print(texto[-1])
```

Saída:

```text
P
n
```

O índice `0` acessa o primeiro caractere. O índice `-1` acessa o último.

## 4.6 — Slicing

Slicing, ou fatiamento, extrai uma parte da string.

```python
texto = "Python"
print(texto[1:4])
```

Saída:

```text
yth
```

A sintaxe geral é:

```python
sequencia[inicio:fim:passo]
```

O índice de início é incluído. O índice de fim é excluído.

## 4.7 — Exemplos de slicing

```python
texto = "Python"

print(texto[:3])
print(texto[::2])
print(texto[::-1])
```

Saída:

```text
Pyt
Pto
nohtyP
```

No último exemplo, `[::-1]` percorre a string de trás para frente.

## 4.8 — Métodos de limpeza e caixa

```python
exemplo = " Olá Mundo "

print(exemplo.strip())
print(exemplo.lower())
print(exemplo.upper())
print(exemplo.replace("Mundo", "Python").strip())
```

Saída:

```text
Olá Mundo
 olá mundo 
 OLÁ MUNDO 
Olá Python
```

- `.strip()` remove espaços no início e no fim;
- `.lower()` transforma em minúsculas;
- `.upper()` transforma em maiúsculas;
- `.replace()` substitui uma parte do texto por outra.

## 4.9 — Verificação de conteúdo

```python
texto = "Python é poderoso"

print("poderoso" in texto)
print("Java" in texto)
print(texto.startswith("Py"))
print(texto.endswith("poderoso"))
```

Saída:

```text
True
False
True
True
```

O operador `in` verifica se uma substring está presente. Já `startswith()` e `endswith()` verificam o começo e o final da string.

## 4.10 — O que pode dar errado?

Strings são imutáveis. Isso significa que você não pode alterar um caractere diretamente.

```python
texto = "Python"
texto[0] = "J"
```

Esse código gera erro. Para criar outro texto, use concatenação, slicing ou métodos de string:

```python
texto = "Python"
novo_texto = "J" + texto[1:]
print(novo_texto)
```

Saída:

```text
Jython
```

## 4.11 — Resumo do capítulo

Neste capítulo, vimos como criar strings, usar f-strings, acessar caracteres com índices, extrair partes com slicing e aplicar métodos comuns como `strip()`, `lower()`, `upper()` e `replace()`.

## 4.12 — Exercícios

1. Crie uma string com seu nome e imprima a primeira letra.
2. Inverta uma palavra usando slicing.
3. Remova espaços extras de uma string usando `.strip()`.
4. Verifique se uma frase contém a palavra `Python`.
5. Formate um preço com duas casas decimais usando f-string.

## 4.13 — Desafio

Receba um e-mail via `input()` e verifique se ele contém `@` e termina com `.com`.

---

# Capítulo 5 — Condicionais e operadores

Condicionais permitem que um programa tome decisões. Em vez de executar sempre o mesmo caminho, o programa pode escolher uma ação com base em uma condição.

Ao final deste capítulo, você será capaz de:

- usar `if`, `elif` e `else`;
- usar operadores de comparação;
- usar operadores lógicos;
- entender o fluxo de execução de uma condicional;
- reconhecer erros comuns de indentação e comparação.

## 5.1 — O problema

Imagine um sistema que precisa permitir a entrada de uma pessoa apenas se ela tiver pelo menos 18 anos. Sem condicionais, o programa não conseguiria tomar essa decisão.

## 5.2 — Estrutura `if`

```python
idade = 18

if idade >= 18:
    print("Entrada permitida")
```

O bloco dentro do `if` só será executado se a condição for verdadeira.

## 5.3 — Estrutura `if/else`

```python
idade = 16

if idade >= 18:
    print("Entrada permitida")
else:
    print("Entrada negada")
```

Se a condição do `if` for falsa, o bloco `else` será executado.

## 5.4 — Estrutura `if/elif/else`

```python
nome = "Latam"

if nome == "Alura":
    print("Bem-vindo à Alura!")
elif nome == "Latam":
    print("Bem-vindo ao Latam!")
else:
    print("Nome desconhecido.")
```

O `elif` permite testar múltiplas condições em sequência. O primeiro bloco cuja condição for verdadeira será executado.

## 5.5 — Operadores de comparação

| Operador | Descrição | Exemplo |
|---|---|---|
| `==` | igual a | `x == 10` |
| `!=` | diferente de | `x != 10` |
| `>` | maior que | `x > 10` |
| `<` | menor que | `x < 10` |
| `>=` | maior ou igual a | `x >= 10` |
| `<=` | menor ou igual a | `x <= 10` |

## 5.6 — Operadores lógicos

Operadores lógicos combinam condições.

### `and`

Retorna `True` apenas se todas as condições forem verdadeiras.

```python
idade = 25
tem_documento = True

if idade >= 18 and tem_documento:
    print("Entrada permitida")
else:
    print("Entrada negada")
```

### `or`

Retorna `True` se pelo menos uma condição for verdadeira.

```python
feriado = False
folga = True

if feriado or folga:
    print("Você pode descansar hoje")
else:
    print("Dia normal de trabalho")
```

### `not`

Inverte o valor lógico.

```python
usuario_bloqueado = False

if not usuario_bloqueado:
    print("Acesso permitido")
```

## 5.7 — O que pode dar errado?

Um erro comum é usar `=` em vez de `==`.

Errado:

```python
if idade = 18:
    print("Maior de idade")
```

Correto:

```python
if idade == 18:
    print("Tem exatamente 18 anos")
```

O operador `=` atribui valor. O operador `==` compara valores.

## 5.8 — Um pouco mais: comparação encadeada

Python permite comparações encadeadas:

```python
idade = 20

if 18 <= idade <= 60:
    print("Adulto")
```

Isso é equivalente a:

```python
if idade >= 18 and idade <= 60:
    print("Adulto")
```

## 5.9 — Resumo do capítulo

Neste capítulo, vimos que condicionais permitem controlar o fluxo do programa. Usamos `if`, `elif` e `else`, além de operadores de comparação e operadores lógicos.

## 5.10 — Exercícios

1. Peça uma idade e diga se a pessoa é maior de idade.
2. Peça uma nota e diga se o aluno foi aprovado, em recuperação ou reprovado.
3. Verifique se um número é positivo, negativo ou zero.
4. Crie uma regra de acesso usando idade e documento.

## 5.11 — Desafio

Crie um programa que simule um pequeno sistema de login. O usuário deve informar login e senha. O acesso só deve ser permitido se os dois valores estiverem corretos.

---

# Capítulo 6 — Estrutura condicional `match/case`

A estrutura `match/case` foi introduzida no Python 3.10 e permite comparar valores com padrões. Ela é especialmente útil quando precisamos lidar com diferentes formatos de dados.

Ao final deste capítulo, você será capaz de:

- entender a ideia de pattern matching;
- usar `match` com valores simples;
- usar `match` com tuplas e dicionários;
- entender o papel do `case _`;
- escolher entre `if/elif/else` e `match/case`.

## 6.1 — O problema

Às vezes, uma variável pode assumir vários formatos diferentes. Por exemplo, um comando pode ser uma string simples, uma tupla com dados ou um dicionário com campos específicos.

Com muitos `if/elif`, o código pode ficar grande e difícil de ler. O `match/case` ajuda a expressar esses casos de forma mais clara.

## 6.2 — Exemplo básico

```python
def processar_comando(comando):
    match comando:
        case "iniciar":
            print("Sistema iniciando...")
        case "parar":
            print("Sistema parando...")
        case _:
            print("Comando não reconhecido.")

processar_comando("iniciar")
```

Saída:

```text
Sistema iniciando...
```

O `case _` funciona como um caso padrão, parecido com o `else`.

## 6.3 — Desestruturando tuplas

```python
def processar_comando(comando):
    match comando:
        case ("enviar", destinatario):
            print(f"Enviando dados para {destinatario}...")
        case _:
            print("Comando inválido")

processar_comando(("enviar", "servidor_central"))
```

Saída:

```text
Enviando dados para servidor_central...
```

Nesse caso, o Python verifica se o valor é uma tupla com dois elementos, sendo o primeiro `"enviar"`. Se for, o segundo elemento é atribuído automaticamente à variável `destinatario`.

## 6.4 — Desestruturando dicionários

```python
def processar_comando(comando):
    match comando:
        case {"acao": "deletar", "id": item_id}:
            print(f"Deletando item {item_id}.")
        case _:
            print("Comando inválido")

processar_comando({"acao": "deletar", "id": 101})
```

Saída:

```text
Deletando item 101.
```

Aqui, o Python verifica se o dicionário possui a chave `"acao"` com valor `"deletar"` e uma chave `"id"`. O valor de `"id"` é capturado em `item_id`.

## 6.5 — Quando usar `match/case`?

Use `match/case` quando:

- existem vários formatos possíveis para o mesmo dado;
- você precisa extrair valores de tuplas, listas ou dicionários;
- uma sequência longa de `if/elif` está deixando o código confuso;
- você está trabalhando com comandos, eventos ou mensagens estruturadas.

Para condições simples, `if/elif/else` continua sendo suficiente.

## 6.6 — O que pode dar errado?

Um erro comum é tentar usar `match/case` em versões antigas do Python.

```text
SyntaxError: invalid syntax
```

Se isso acontecer, verifique a versão:

```bash
python --version
```

O `match/case` exige Python 3.10 ou superior.

## 6.7 — Resumo do capítulo

Neste capítulo, vimos que `match/case` permite comparar valores com padrões e desestruturar dados. Ele é útil para comandos, eventos, mensagens e estruturas mais complexas.

## 6.8 — Exercícios

1. Crie uma função que receba um comando: `"abrir"`, `"fechar"` ou outro valor.
2. Use `match/case` para tratar cada comando.
3. Crie um caso que receba uma tupla como `("buscar", "produto")`.
4. Crie um caso que receba um dicionário com `{"acao": "criar", "nome": "Ana"}`.

## 6.9 — Desafio

Crie um pequeno interpretador de comandos para uma aplicação de tarefas. Ele deve aceitar comandos como `"listar"`, `("adicionar", "Estudar Python")` e `{"acao": "remover", "id": 1}`.

---

# Capítulo 7 — Laços de repetição

Laços permitem executar um bloco de código várias vezes. Em Python, as principais estruturas são `for` e `while`.

Ao final deste capítulo, você será capaz de:

- usar `for` para percorrer iteráveis;
- usar `while` para repetir enquanto uma condição for verdadeira;
- usar `range()`;
- controlar saída com `print()`;
- usar `break` e `continue`.

## 7.1 — O problema

Imagine que você tem uma lista com cem nomes e precisa imprimir todos. Escrever cem linhas de `print()` seria repetitivo e difícil de manter.

Laços resolvem esse problema permitindo executar a mesma ação para vários itens.

## 7.2 — Laço `for`

O `for` percorre os elementos de um iterável, como lista, tupla, string ou dicionário.

```python
nomes = ["Carlos", "Ana", "Pedro", "Maria"]

for nome in nomes:
    print(nome)
```

A cada repetição, a variável `nome` recebe um elemento da lista.

## 7.3 — Controle da saída com `print()`

Por padrão, `print()` quebra linha ao final. Podemos alterar isso com o parâmetro `end`.

```python
nomes = ["Carlos", "Ana", "Pedro"]

print("Nomes:", end=" ")
for nome in nomes:
    print(nome, end=", ")
```

Saída:

```text
Nomes: Carlos, Ana, Pedro,
```

Esse exemplo mantém tudo na mesma linha, mas deixa uma vírgula no final. Uma forma mais elegante é usar `join()`:

```python
nomes = ["Carlos", "Ana", "Pedro"]
print("Nomes:", ", ".join(nomes))
```

Saída:

```text
Nomes: Carlos, Ana, Pedro
```

## 7.4 — Caracteres de escape

Caracteres de escape ajudam a formatar texto.

| Caractere | Significado |
|---|---|
| `\n` | nova linha |
| `\t` | tabulação horizontal |
| `\\` | barra invertida |
| `\"` | aspas duplas dentro de string |

Exemplo:

```python
produtos = [
    {"id": 1, "nome": "Teclado", "preco": 150.00},
    {"id": 2, "nome": "Mouse", "preco": 89.90},
]

print("ID\tProduto\tPreço")
for produto in produtos:
    print(f'{produto["id"]}\t{produto["nome"]}\tR$ {produto["preco"]:.2f}')
```

## 7.5 — Laço `while`

O `while` executa um bloco enquanto uma condição for verdadeira.

```python
contador = 0

while contador < 5:
    print(f"Contador atual: {contador}")
    contador += 1
```

O laço termina quando `contador < 5` se torna falso.

## 7.6 — Cuidado com loop infinito

Se a condição nunca ficar falsa, o laço não termina.

```python
contador = 0

while contador < 5:
    print(contador)
```

Esse código é problemático porque `contador` nunca muda. A correção é incrementar o contador:

```python
contador = 0

while contador < 5:
    print(contador)
    contador += 1
```

## 7.7 — `break`

A instrução `break` encerra o laço imediatamente.

```python
nomes = ["PM3", "Alura", "Latam", "Outros"]

for nome in nomes:
    print(nome)
    if nome == "Alura":
        print("Nome encontrado! Saindo do laço.")
        break
```

Quando encontra `"Alura"`, o laço é interrompido.

## 7.8 — `continue`

A instrução `continue` pula a iteração atual e vai para a próxima.

```python
nomes = ["PM3", "Alura", "Latam", "Outros"]

for nome in nomes:
    if nome == "Alura":
        print("Ignorando Alura.")
        continue

    print(f"Nome: {nome}")
```

Quando `nome` é `"Alura"`, o segundo `print()` não é executado naquela iteração.

## 7.9 — Iterando com `range()`

A função `range()` gera uma sequência de números.

### `range(stop)`

```python
for i in range(5):
    print(i)
```

Saída:

```text
0
1
2
3
4
```

O valor de parada é exclusivo. `range(5)` vai de `0` até `4`.

### `range(start, stop)`

```python
for numero in range(1, 6):
    print(numero)
```

Saída:

```text
1
2
3
4
5
```

### `range(start, stop, step)`

```python
for i in range(2, 11, 2):
    print(i)
```

Saída:

```text
2
4
6
8
10
```

### Contagem regressiva

```python
for segundos in range(10, 0, -1):
    print(f"{segundos}...")

print("Lançar!")
```

## 7.10 — Um pouco mais: `enumerate()`

Quando você precisa do índice e do valor, use `enumerate()`.

```python
nomes = ["Ana", "Pedro", "Maria"]

for indice, nome in enumerate(nomes):
    print(indice, nome)
```

Saída:

```text
0 Ana
1 Pedro
2 Maria
```

## 7.11 — Resumo do capítulo

Neste capítulo, vimos que `for` é ideal para percorrer iteráveis e `while` é útil quando a repetição depende de uma condição. Também vimos `range()`, `break`, `continue`, `end`, caracteres de escape e `enumerate()`.

## 7.12 — Exercícios

1. Imprima os números de 1 a 10 usando `for`.
2. Imprima os números pares de 2 a 20 usando `range()`.
3. Percorra uma lista de nomes e pare quando encontrar um nome específico.
4. Use `continue` para ignorar números ímpares.
5. Refaça um laço `for` usando `while`.

## 7.13 — Desafio

Crie um programa que peça números ao usuário até ele digitar `0`. Ao final, mostre a soma dos números digitados.

---

# Capítulo 8 — Funções em Python

Funções são blocos de código reutilizáveis que executam uma tarefa específica. Elas ajudam a organizar o programa, evitar repetição e separar responsabilidades.

Ao final deste capítulo, você será capaz de:

- declarar funções com `def`;
- diferenciar parâmetro e argumento;
- retornar valores com `return`;
- usar parâmetros padrão;
- entender lambdas, recursão e closures;
- reconhecer quando criar uma função.

## 8.1 — O problema

Sem funções, todo código fica concentrado em um único bloco. Isso gera repetição, dificulta testes e torna o programa mais difícil de manter.

Funções permitem dar nome a uma lógica e reutilizá-la sempre que necessário.

## 8.2 — Criando uma função

```python
def ola_mundo(nome):
    return f"Olá, {nome}!"

mensagem = ola_mundo("Laís")
print(mensagem)
```

Saída:

```text
Olá, Laís!
```

## 8.3 — Parâmetros e argumentos

```python
def ola_mundo(nome):
    return f"Olá, {nome}!"
```

`nome` é um parâmetro. Ele funciona como uma variável interna da função.

```python
ola_mundo("Laís")
```

`"Laís"` é o argumento. É o valor real passado para o parâmetro.

## 8.4 — `return`

A instrução `return` devolve um valor para quem chamou a função.

```python
def somar(a, b):
    return a + b

resultado = somar(2, 3)
print(resultado)
```

Saída:

```text
5
```

Sem `return`, a função retorna `None` por padrão.

```python
def mostrar_mensagem():
    print("Olá")

resultado = mostrar_mensagem()
print(resultado)
```

Saída:

```text
Olá
None
```

## 8.5 — Parâmetros padrão

Parâmetros padrão tornam um argumento opcional.

```python
def cumprimentar(nome="Visitante"):
    print(f"Olá, {nome}!")

cumprimentar()
cumprimentar("Jade")
```

Saída:

```text
Olá, Visitante!
Olá, Jade!
```

## 8.6 — Escopo local

Variáveis criadas dentro de uma função existem apenas dentro dela.

```python
def calcular():
    resultado = 10 + 5
    return resultado

print(calcular())
```

A variável `resultado` foi criada dentro da função. Fora dela, esse nome não existe.

## 8.7 — Funções lambda

Uma função lambda é uma função pequena e anônima, geralmente usada para expressões simples.

```python
soma = lambda a, b: a + b
print(soma(3, 5))
```

Saída:

```text
8
```

Lambdas são úteis em funções como `map()`, `filter()` e ordenações com `key`.

```python
nomes = ["Carlos", "Ana", "Pedro"]
ordenados = sorted(nomes, key=lambda nome: len(nome))
print(ordenados)
```

Saída:

```text
['Ana', 'Pedro', 'Carlos']
```

## 8.8 — Funções recursivas

Uma função recursiva chama a si mesma. Ela precisa ter uma condição de parada.

```python
def fatorial(n):
    if n == 0:
        return 1
    return n * fatorial(n - 1)

print(fatorial(5))
```

Saída:

```text
120
```

A condição `if n == 0` impede que a função chame a si mesma para sempre.

## 8.9 — Closures

Uma closure é uma função interna que lembra variáveis do escopo onde foi criada.

```python
def multiplicador(n):
    def multiplica(x):
        return x * n
    return multiplica

triplo = multiplicador(3)
print(triplo(5))
```

Saída:

```text
15
```

A função `multiplica` continua lembrando o valor de `n`, mesmo depois que `multiplicador` terminou.

## 8.10 — Quando criar uma função?

Crie uma função quando:

- a mesma lógica aparece mais de uma vez;
- um trecho de código tem uma responsabilidade clara;
- você quer deixar o código mais legível;
- você quer facilitar testes;
- você quer separar entrada, processamento e saída.

## 8.11 — O que pode dar errado?

Um erro comum é esquecer de chamar a função com parênteses.

```python
def saudacao():
    return "Olá"

print(saudacao)
```

Esse código imprime uma representação da função, não o resultado dela.

Correto:

```python
print(saudacao())
```

## 8.12 — Resumo do capítulo

Neste capítulo, vimos que funções organizam código e evitam repetição. Estudamos `def`, parâmetros, argumentos, `return`, parâmetros padrão, lambdas, recursão e closures.

## 8.13 — Exercícios

1. Crie uma função que receba dois números e retorne a soma.
2. Crie uma função que receba um nome e retorne uma saudação.
3. Crie uma função com parâmetro padrão.
4. Crie uma função que calcule o dobro de um número usando lambda.
5. Crie uma função recursiva que conte de `n` até zero.

## 8.14 — Desafio

Crie uma função chamada `calcular_media(notas)` que receba uma lista de notas e retorne a média. Depois, crie outra função que diga se o aluno foi aprovado ou reprovado.

---

# Capítulo 9 — Built-in functions em Python

Built-in functions são funções já disponíveis no Python sem a necessidade de importar módulos. Elas fazem parte da linguagem e ajudam em tarefas comuns como imprimir, converter tipos, medir tamanhos, ordenar dados, aplicar funções e validar informações.

Ao final deste capítulo, você será capaz de:

- entender o que são built-in functions;
- usar funções nativas em situações reais;
- diferenciar funções de conversão, inspeção, cálculo e iteração;
- evitar usos confusos de `map()`, `filter()` e `zip()`;
- escolher funções nativas em vez de escrever código desnecessário.

## 9.1 — O problema

Muitas tarefas aparecem o tempo todo em programas: saber o tamanho de uma lista, converter uma string para número, somar valores, ordenar dados ou verificar se todos os itens atendem a uma condição.

Sem built-in functions, você teria que escrever essas lógicas manualmente. Com elas, o código fica menor, mais claro e mais idiomático.

## 9.2 — O que são built-in functions?

São funções disponíveis automaticamente no Python. Você pode chamá-las diretamente:

```python
print("Olá")
len("Python")
type(10)
```

Não é necessário fazer `import`.

## 9.3 — Entrada, saída e inspeção

| Função | Para que serve | Exemplo |
|---|---|---|
| `print()` | exibe valores no console | `print("Olá")` |
| `input()` | lê entrada do usuário como string | `input("Nome: ")` |
| `type()` | retorna o tipo de um objeto | `type(10)` |
| `isinstance()` | verifica se um objeto é de um tipo | `isinstance(10, int)` |
| `id()` | retorna um identificador do objeto | `id(valor)` |
| `dir()` | lista atributos e métodos disponíveis | `dir(str)` |
| `help()` | mostra ajuda/documentação | `help(len)` |

Exemplo prático:

```python
valor = "123"

print(type(valor))
print(isinstance(valor, str))
```

Saída:

```text
<class 'str'>
True
```

## 9.4 — Conversão de tipos

| Função | Conversão |
|---|---|
| `int()` | converte para inteiro |
| `float()` | converte para número decimal |
| `str()` | converte para string |
| `bool()` | converte para booleano |
| `list()` | converte para lista |
| `tuple()` | converte para tupla |
| `set()` | converte para conjunto |
| `dict()` | cria/converte para dicionário quando a estrutura é compatível |

Exemplo:

```python
idade_texto = "25"
idade = int(idade_texto)

print(idade + 1)
```

Saída:

```text
26
```

## 9.5 — Funções numéricas

| Função | Para que serve | Exemplo |
|---|---|---|
| `abs()` | retorna o valor absoluto | `abs(-10)` |
| `round()` | arredonda um número | `round(3.1415, 2)` |
| `sum()` | soma itens de um iterável | `sum([1, 2, 3])` |
| `min()` | retorna o menor valor | `min([3, 1, 5])` |
| `max()` | retorna o maior valor | `max([3, 1, 5])` |
| `pow()` | calcula potência | `pow(2, 3)` |
| `divmod()` | retorna quociente e resto | `divmod(10, 3)` |

Exemplo:

```python
notas = [7.5, 8.0, 9.0]
media = sum(notas) / len(notas)

print(f"Média: {media:.2f}")
```

Saída:

```text
Média: 8.17
```

## 9.6 — Funções para iteráveis

Iteráveis são objetos que podem ser percorridos, como listas, tuplas, strings, dicionários, conjuntos e objetos retornados por `range()`.

| Função | Para que serve | Exemplo |
|---|---|---|
| `len()` | retorna quantidade de itens | `len([1, 2, 3])` |
| `range()` | gera sequência numérica | `range(5)` |
| `enumerate()` | gera índice e valor | `enumerate(nomes)` |
| `zip()` | combina iteráveis | `zip(nomes, idades)` |
| `sorted()` | retorna uma lista ordenada | `sorted([3, 1, 2])` |
| `reversed()` | retorna iterador reverso | `reversed([1, 2, 3])` |
| `map()` | aplica função a cada item | `map(str, [1, 2, 3])` |
| `filter()` | filtra itens por condição | `filter(funcao, lista)` |
| `any()` | verifica se algum item é verdadeiro | `any([False, True])` |
| `all()` | verifica se todos são verdadeiros | `all([True, True])` |

## 9.7 — `enumerate()`

Use `enumerate()` quando precisar do índice e do valor.

```python
nomes = ["Ana", "Pedro", "Maria"]

for indice, nome in enumerate(nomes, start=1):
    print(indice, nome)
```

Saída:

```text
1 Ana
2 Pedro
3 Maria
```

## 9.8 — `zip()`

Use `zip()` para combinar iteráveis posição por posição.

```python
nomes = ["Ana", "Pedro", "Maria"]
idades = [25, 30, 22]

for nome, idade in zip(nomes, idades):
    print(f"{nome} tem {idade} anos")
```

Saída:

```text
Ana tem 25 anos
Pedro tem 30 anos
Maria tem 22 anos
```

Se os iteráveis tiverem tamanhos diferentes, `zip()` para no menor.

## 9.9 — `map()`

`map()` aplica uma função a cada item de um iterável.

```python
numeros = [1, 2, 3]
dobrados = list(map(lambda x: x * 2, numeros))

print(dobrados)
```

Saída:

```text
[2, 4, 6]
```

Em muitos casos, list comprehension pode ser mais legível:

```python
dobrados = [x * 2 for x in numeros]
```

## 9.10 — `filter()`

`filter()` mantém apenas os itens que passam em uma condição.

```python
numeros = [1, 2, 3, 4, 5]
pares = list(filter(lambda x: x % 2 == 0, numeros))

print(pares)
```

Saída:

```text
[2, 4]
```

Também podemos escrever com list comprehension:

```python
pares = [x for x in numeros if x % 2 == 0]
```

## 9.11 — `any()` e `all()`

`any()` retorna `True` se pelo menos um item for verdadeiro.

```python
permissoes = [False, False, True]
print(any(permissoes))
```

Saída:

```text
True
```

`all()` retorna `True` apenas se todos os itens forem verdadeiros.

```python
campos_preenchidos = [True, True, True]
print(all(campos_preenchidos))
```

Saída:

```text
True
```

Exemplo prático:

```python
senha = "Python123"

tem_tamanho = len(senha) >= 8
tem_numero = any(caractere.isdigit() for caractere in senha)

def senha_valida(senha):
    return len(senha) >= 8 and any(c.isdigit() for c in senha)

print(senha_valida(senha))
```

## 9.12 — `sorted()` e `reversed()`

`sorted()` retorna uma nova lista ordenada.

```python
numeros = [3, 1, 4, 2]
ordenados = sorted(numeros)

print(numeros)
print(ordenados)
```

Saída:

```text
[3, 1, 4, 2]
[1, 2, 3, 4]
```

`reversed()` retorna um iterador reverso.

```python
numeros = [1, 2, 3]
invertidos = list(reversed(numeros))

print(invertidos)
```

Saída:

```text
[3, 2, 1]
```

## 9.13 — `callable()`

`callable()` verifica se um objeto pode ser chamado como função.

```python
def somar(a, b):
    return a + b

numero = 10

print(callable(somar))
print(callable(numero))
```

Saída:

```text
True
False
```

## 9.14 — O que pode dar errado?

### Usar `map()` e esquecer de converter para lista

```python
numeros = [1, 2, 3]
dobrados = map(lambda x: x * 2, numeros)

print(dobrados)
```

A saída será algo como:

```text
<map object at ...>
```

Isso acontece porque `map()` retorna um iterador. Para visualizar como lista:

```python
print(list(dobrados))
```

### Usar `filter()` sem entender a condição

```python
valores = [0, 1, "", "Python", [], [1, 2]]
filtrados = list(filter(None, valores))

print(filtrados)
```

Saída:

```text
[1, 'Python', [1, 2]]
```

Quando a função é `None`, `filter()` remove valores considerados falsos.

## 9.15 — Resumo do capítulo

Built-in functions são recursos nativos do Python que reduzem código repetitivo e tornam o programa mais expressivo. Neste capítulo, vimos funções de entrada e saída, inspeção, conversão, cálculo e manipulação de iteráveis.

## 9.16 — Exercícios

1. Use `len()` para contar caracteres de uma string.
2. Use `sum()` e `len()` para calcular a média de uma lista.
3. Use `enumerate()` para imprimir uma lista numerada.
4. Use `zip()` para combinar nomes e notas.
5. Use `map()` para dobrar números.
6. Use `filter()` para selecionar números pares.
7. Use `any()` para verificar se uma senha contém número.
8. Use `all()` para verificar se todos os campos obrigatórios foram preenchidos.

## 9.17 — Desafio

Crie uma função que receba uma lista de produtos no formato abaixo e retorne apenas os produtos com preço maior que 100, ordenados do mais barato para o mais caro.

```python
produtos = [
    {"nome": "Teclado", "preco": 150.00},
    {"nome": "Mouse", "preco": 89.90},
    {"nome": "Monitor", "preco": 900.00},
]
```

Use pelo menos duas built-in functions no seu código.

---

# Capítulo 10 — Expressões regulares

Expressões regulares, também chamadas de regex, são padrões usados para encontrar, validar ou substituir trechos de texto.

Ao final deste capítulo, você será capaz de:

- entender o que é uma expressão regular;
- usar o módulo `re`;
- reconhecer metacaracteres comuns;
- usar `search()`, `match()`, `findall()` e `sub()`;
- criar padrões simples para e-mail, telefone e data.

## 10.1 — O problema

Em sistemas reais, é comum validar ou extrair informações de textos. Alguns exemplos:

- verificar se um e-mail parece válido;
- encontrar números em uma frase;
- substituir dígitos por `#`;
- extrair datas de um texto;
- validar formatos como CPF, telefone ou CEP.

Regex ajuda a resolver esse tipo de problema.

## 10.2 — Importando o módulo `re`

```python
import re
```

O módulo `re` faz parte da biblioteca padrão do Python. Não é necessário instalar nada.

## 10.3 — Caracteres literais

Um caractere literal corresponde a ele mesmo.

```python
import re

texto = "Estou estudando Python"
resultado = re.search("Python", texto)

if resultado:
    print("Encontrou")
```

A regex `"Python"` procura exatamente o texto `Python`.

## 10.4 — Metacaracteres comuns

| Símbolo | Descrição |
|---|---|
| `.` | qualquer caractere, exceto nova linha |
| `\d` | qualquer dígito de 0 a 9 |
| `\D` | qualquer caractere que não seja dígito |
| `\w` | caractere alfanumérico ou `_` |
| `\W` | caractere que não é alfanumérico |
| `\s` | espaço em branco |
| `\S` | caractere que não é espaço em branco |

## 10.5 — Classes de caracteres

| Símbolo | Descrição |
|---|---|
| `[abc]` | corresponde a `a`, `b` ou `c` |
| `[^abc]` | corresponde a qualquer caractere exceto `a`, `b` ou `c` |
| `[a-z]` | letras minúsculas de `a` a `z` |
| `[A-Z]` | letras maiúsculas de `A` a `Z` |
| `[0-9]` | dígitos de `0` a `9` |

## 10.6 — Quantificadores

| Símbolo | Descrição |
|---|---|
| `*` | zero ou mais ocorrências |
| `+` | uma ou mais ocorrências |
| `?` | zero ou uma ocorrência |
| `{n}` | exatamente `n` ocorrências |
| `{n,}` | `n` ou mais ocorrências |
| `{n,m}` | entre `n` e `m` ocorrências |

## 10.7 — Raw strings

Em regex, é recomendado usar raw strings com o prefixo `r`.

```python
padrao = r"\d{2}/\d{2}/\d{4}"
```

Isso evita conflitos com barras invertidas usadas pelo próprio Python.

## 10.8 — Exemplo: encontrando e-mail

```python
import re

texto = "Entre em contato pelo email support@example.com"
padrao_email = r"[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}"

resultado = re.search(padrao_email, texto)

if resultado:
    print("Email encontrado:", resultado.group())
else:
    print("Nenhum email encontrado.")
```

Saída:

```text
Email encontrado: support@example.com
```

## 10.9 — Métodos úteis do módulo `re`

| Método | Descrição | Exemplo |
|---|---|---|
| `re.search()` | procura em qualquer parte da string | `re.search(r"\d+", texto)` |
| `re.match()` | verifica apenas no início da string | `re.match(r"abc", "abcdef")` |
| `re.findall()` | retorna todas as ocorrências | `re.findall(r"\d+", texto)` |
| `re.sub()` | substitui ocorrências | `re.sub(r"\d", "#", texto)` |

Exemplo com `findall()`:

```python
import re

texto = "3 gatos e 2 cachorros"
numeros = re.findall(r"\d+", texto)

print(numeros)
```

Saída:

```text
['3', '2']
```

Exemplo com `sub()`:

```python
import re

texto = "Meu número é 1234"
mascarado = re.sub(r"\d", "#", texto)

print(mascarado)
```

Saída:

```text
Meu número é ####
```

## 10.10 — Exemplos de padrões

### Data no formato `dd/mm/aaaa`

```python
padrao_data = r"\b\d{2}/\d{2}/\d{4}\b"
```

Esse padrão encontra datas como `25/12/2025`.

### Telefone simples

```python
padrao_telefone = r"\(\d{2}\)\s\d{4,5}-\d{4}"
```

Esse padrão encontra telefones como `(11) 98765-4321`.

## 10.11 — O que pode dar errado?

Regex pode ficar difícil de ler quando tenta resolver tudo de uma vez. Um padrão muito complexo pode ser difícil de manter e pode aceitar valores inválidos sem você perceber.

Por exemplo, uma regex simples de e-mail pode validar o formato geral, mas não garante que o e-mail realmente exista.

Também tome cuidado com `re.match()`: ele verifica apenas o início da string. Se você quer procurar em qualquer posição, use `re.search()`.

## 10.12 — Resumo do capítulo

Neste capítulo, vimos que regex é usada para encontrar, validar e substituir padrões em texto. Estudamos metacaracteres, classes, quantificadores, raw strings e métodos importantes do módulo `re`.

## 10.13 — Exercícios

1. Use regex para encontrar todos os números em uma frase.
2. Use `re.sub()` para trocar todos os dígitos de um CPF por `*`.
3. Crie uma regex simples para encontrar datas no formato `dd/mm/aaaa`.
4. Teste a diferença entre `re.match()` e `re.search()`.

## 10.14 — Desafio

Crie uma função `extrair_emails(texto)` que receba um texto e retorne uma lista com todos os e-mails encontrados.

---

# Capítulo 11 — Dict comprehension

Dict comprehension é uma forma compacta de criar dicionários a partir de iteráveis. Ela segue a mesma ideia de list comprehension, mas gera pares chave-valor.

Ao final deste capítulo, você será capaz de:

- entender a estrutura de uma dict comprehension;
- transformar dados de um dicionário em outro;
- aplicar condições dentro da comprehension;
- evitar comprehensions difíceis de ler.

## 11.1 — O problema

Muitas vezes precisamos criar um novo dicionário a partir de outro. Por exemplo, aplicar desconto em produtos, transformar nomes em letras minúsculas ou filtrar itens com base em uma regra.

Sem dict comprehension, podemos fazer isso com um `for` tradicional. Com dict comprehension, podemos expressar a transformação de forma mais direta.

## 11.2 — Estrutura básica

```python
novo_dicionario = {chave: valor for item in iteravel}
```

Exemplo simples:

```python
numeros = [1, 2, 3]
quadrados = {numero: numero ** 2 for numero in numeros}

print(quadrados)
```

Saída:

```text
{1: 1, 2: 4, 3: 9}
```

## 11.3 — Exemplo com estoque

```python
estoque = {
    "Arroz": {"quantidade": 30, "preco": 25.00},
    "Feijão": {"quantidade": 15, "preco": 18.50},
    "Macarrão": {"quantidade": 50, "preco": 10.00},
    "Óleo": {"quantidade": 10, "preco": 22.00},
    "Açúcar": {"quantidade": 40, "preco": 8.00},
    "Café": {"quantidade": 5, "preco": 30.00},
}
```

Esse dicionário representa produtos. Cada chave é o nome do produto e cada valor é outro dicionário com `quantidade` e `preco`.

## 11.4 — Aplicando desconto

```python
estoque_com_desconto = {
    nome: {
        "quantidade": dados["quantidade"],
        "preco": dados["preco"] * 0.9 if dados["preco"] > 20 else dados["preco"],
    }
    for nome, dados in estoque.items()
}

print(estoque_com_desconto)
```

Nesse exemplo, aplicamos 10% de desconto aos produtos com preço maior que 20.

## 11.5 — O que aconteceu no código?

O trecho:

```python
for nome, dados in estoque.items()
```

percorre os pares do dicionário `estoque`. Em cada iteração:

- `nome` recebe o nome do produto;
- `dados` recebe o dicionário com quantidade e preço.

O trecho:

```python
dados["preco"] * 0.9 if dados["preco"] > 20 else dados["preco"]
```

é uma expressão condicional. Se o preço for maior que 20, aplica desconto. Caso contrário, mantém o preço original.

## 11.6 — Filtrando itens

Também podemos usar `if` no final da comprehension para filtrar itens.

```python
produtos_caros = {
    nome: dados
    for nome, dados in estoque.items()
    if dados["preco"] > 20
}

print(produtos_caros)
```

Nesse caso, o novo dicionário terá apenas produtos com preço maior que 20.

## 11.7 — Quando não usar dict comprehension?

Evite dict comprehension quando a lógica ficar muito grande. Se houver muitos `if`, cálculos complexos ou regras de negócio extensas, um `for` tradicional pode ser mais legível.

Menos legível:

```python
resultado = {k: v * 2 if v > 10 else v + 1 for k, v in dados.items() if v != 0}
```

Mais legível:

```python
resultado = {}

for chave, valor in dados.items():
    if valor == 0:
        continue

    if valor > 10:
        resultado[chave] = valor * 2
    else:
        resultado[chave] = valor + 1
```

## 11.8 — Resumo do capítulo

Dict comprehension permite criar dicionários de forma compacta. Ela é excelente para transformações simples e filtros diretos, mas deve ser evitada quando prejudicar a legibilidade.

## 11.9 — Exercícios

1. Crie um dicionário com números de 1 a 5 como chave e seus quadrados como valor.
2. A partir de um dicionário de produtos, crie outro apenas com produtos acima de determinado preço.
3. Aplique 5% de desconto em produtos com preço maior que 100.
4. Transforme todas as chaves de um dicionário para letras minúsculas.

## 11.10 — Desafio

Crie uma dict comprehension que receba um dicionário de alunos e notas e retorne outro dicionário com o status `"aprovado"` ou `"reprovado"` para cada aluno.

---

# Capítulo 12 — JSON em Python e uso com APIs

JSON é um formato de texto estruturado muito usado para troca de dados entre sistemas, especialmente em APIs. Ele se parece com dicionários e listas em Python, mas possui regras próprias.

Ao final deste capítulo, você será capaz de:

- entender o que é JSON;
- diferenciar objeto Python de texto JSON;
- serializar dados com `json.dumps()`;
- desserializar dados com `json.loads()`;
- ler e escrever arquivos JSON;
- entender o uso de JSON em APIs.

## 12.1 — O problema

Quando dois sistemas precisam trocar dados, eles precisam usar um formato comum. Por exemplo, uma API pode responder com dados de um usuário, um pedido ou uma transação.

JSON é muito usado porque é texto, é leve e é fácil de ler por pessoas e máquinas.

## 12.2 — JSON parece Python, mas não é Python

Exemplo de JSON:

```json
{
  "nome": "Ana",
  "idade": 25,
  "ativo": true
}
```

Em Python, o equivalente seria:

```python
dados = {
    "nome": "Ana",
    "idade": 25,
    "ativo": True,
}
```

Diferenças importantes:

| JSON | Python |
|---|---|
| `true` | `True` |
| `false` | `False` |
| `null` | `None` |
| texto sempre com aspas duplas | strings podem usar aspas simples ou duplas |

## 12.3 — Importando o módulo `json`

```python
import json
```

O módulo `json` faz parte da biblioteca padrão do Python.

## 12.4 — Serializar com `json.dumps()`

Serializar significa transformar um objeto Python em uma string JSON.

```python
import json

payload = {
    "nome": "João",
    "idade": 30,
    "ativo": True,
}

body = json.dumps(payload, ensure_ascii=False, indent=2)
print(body)
```

Saída:

```json
{
  "nome": "João",
  "idade": 30,
  "ativo": true
}
```

Esse processo é comum ao preparar o corpo de uma requisição para uma API.

## 12.5 — Desserializar com `json.loads()`

Desserializar significa transformar uma string JSON em um objeto Python.

```python
import json

response_text = '{"nome": "João", "idade": 30, "ativo": true}'
dados = json.loads(response_text)

print(dados["nome"])
print(type(dados))
```

Saída:

```text
João
<class 'dict'>
```

Esse processo é comum ao receber o corpo de uma resposta HTTP.

## 12.6 — Lendo arquivo JSON com `json.load()`

```python
import json

with open("dados.json", "r", encoding="utf-8") as arquivo:
    dados = json.load(arquivo)

print(dados)
```

`json.load()` lê JSON a partir de um arquivo aberto.

## 12.7 — Escrevendo arquivo JSON com `json.dump()`

```python
import json

dados = {
    "nome": "Ana",
    "idade": 25,
}

with open("dados.json", "w", encoding="utf-8") as arquivo:
    json.dump(dados, arquivo, ensure_ascii=False, indent=2)
```

`json.dump()` escreve JSON em um arquivo.

## 12.8 — Resumo rápido das funções

| Função | Caminho | Uso |
|---|---|---|
| `json.dumps(obj)` | Python → string JSON | enviar dados para API |
| `json.loads(json_str)` | string JSON → Python | ler resposta de API |
| `json.dump(obj, arquivo)` | Python → arquivo JSON | salvar dados |
| `json.load(arquivo)` | arquivo JSON → Python | carregar dados |

## 12.9 — O que pode dar errado?

### JSON inválido

```python
import json

texto = "{'nome': 'Ana'}"
dados = json.loads(texto)
```

Esse código gera erro porque JSON exige aspas duplas.

Correto:

```python
texto = '{"nome": "Ana"}'
dados = json.loads(texto)
```

### Tentar serializar tipos não suportados diretamente

```python
import json
from datetime import datetime

json.dumps({"agora": datetime.now()})
```

Esse código gera erro porque `datetime` não é serializável por padrão. Uma solução simples é converter para string:

```python
json.dumps({"agora": datetime.now().isoformat()})
```

## 12.10 — JSON e APIs

Em APIs HTTP, JSON costuma aparecer em dois momentos principais:

1. no corpo da requisição, quando o cliente envia dados para o servidor;
2. no corpo da resposta, quando o servidor devolve dados para o cliente.

Exemplo conceitual de payload:

```python
payload = {
    "nome": "João Silva",
    "email": "joao@example.com",
}
```

Esse dicionário pode ser serializado para JSON antes de ser enviado para uma API.

## 12.11 — Resumo do capítulo

Neste capítulo, vimos que JSON é um formato de texto usado para troca de dados. Aprendemos a converter objetos Python para JSON com `json.dumps()` e JSON para objetos Python com `json.loads()`. Também vimos como ler e escrever arquivos JSON.

## 12.12 — Exercícios

1. Crie um dicionário Python com nome, idade e cidade.
2. Converta esse dicionário para string JSON usando `json.dumps()`.
3. Converta uma string JSON para dicionário usando `json.loads()`.
4. Salve um dicionário em um arquivo JSON usando `json.dump()`.
5. Leia um arquivo JSON usando `json.load()`.

## 12.13 — Desafio

Crie um pequeno programa que leia um arquivo `usuarios.json`, filtre apenas usuários ativos e salve o resultado em `usuarios_ativos.json`.

---

# Referências bibliográficas

As referências abaixo foram usadas para revisar, confirmar e complementar o conteúdo desta apostila.

- PYTHON SOFTWARE FOUNDATION. **The Python Tutorial**. Disponível em: <https://docs.python.org/3/tutorial/>. Acesso em: 31 maio 2026.
- PYTHON SOFTWARE FOUNDATION. **Built-in Functions**. Disponível em: <https://docs.python.org/3/library/functions.html>. Acesso em: 31 maio 2026.
- PYTHON SOFTWARE FOUNDATION. **Built-in Types**. Disponível em: <https://docs.python.org/3/library/stdtypes.html>. Acesso em: 31 maio 2026.
- PYTHON SOFTWARE FOUNDATION. **Compound statements**. Disponível em: <https://docs.python.org/3/reference/compound_stmts.html>. Acesso em: 31 maio 2026.
- PYTHON SOFTWARE FOUNDATION. **re — Regular expression operations**. Disponível em: <https://docs.python.org/3/library/re.html>. Acesso em: 31 maio 2026.
- PYTHON SOFTWARE FOUNDATION. **json — JSON encoder and decoder**. Disponível em: <https://docs.python.org/3/library/json.html>. Acesso em: 31 maio 2026.
- PYTHON SOFTWARE FOUNDATION. **PEP 498 — Literal String Interpolation**. Disponível em: <https://peps.python.org/pep-0498/>. Acesso em: 31 maio 2026.
- PYTHON SOFTWARE FOUNDATION. **PEP 634 — Structural Pattern Matching: Specification**. Disponível em: <https://peps.python.org/pep-0634/>. Acesso em: 31 maio 2026.
