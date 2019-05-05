---
description: As estruturas básicas de shell script
---

# Básicos

## Introdução

As principais estruturas da linguagem shell, para rápida referência ao desenvolver scripts.

### Variáveis

Para definir uma variável em um script, defina seu nome e atribua um valor. É comum que variáveis sejam definidas com letrais maiúsculas, mesmo que não sejam constantes como é feito em outras linguagens.

```bash
VARIAVEL="VALOR"
```

O valor de uma variável pode ser acessado adicionando um `$` à frente de seu nome, como em `$VARIAVEL`. Na realidade, existem duas formas de acessar o valor de uma variável:

1. $VARIAVEL
2. ${VARIAVEL}

Esta segunda forma permite concaternar outras strings com o valor da variável, com as chaves atuando como delimitadores do nome da variável.

``` bash
echo "${RANDOM}${RANDOM}."
```

### Imutabilidade

Uma variável pode ser redefinida a não ser que seja definida como imutável. Para tal é possível utilizar duas formas:

``` bash
declare -r VARIAVEL="VALOR"
readonly OUTRA_VARIAVEL="VALOR"
``` 

A vantagem de utilizar `readonly` é que pode-se invocá-la à qualquer momento, não apenas no momento da declaração da variável. Além disso, 

### Argumentos

Pode-se acessar os argumentos de um script através de um conjunto de variáveis especiais:

- $0: armazena o nome do script
- $1: armazena o valor do primeiro argumento
- $2 ... $x: armazena o valor do argumento x.
- $@: Todos os argumentos separados por espaços

Existe, então, uma variável que contêm o valor de cada posição de argumento indicado.











