---
description: As estruturas básicas de shell script
---

# Estruturas básicas

## Introdução

As principais estruturas da linguagem shell, para fácil referência.

## Variáveis 

### Declaração

Para definir uma variável em um script, defina seu nome e atribua um valor. É comum que variáveis sejam definidas com letrais maiúsculas, mesmo que não sejam constantes como é feito em outras linguagens.

```bash
VARIAVEL="VALOR"
```

Variáveis podem começar com underlines ou uma letra. Iniciar o nome de uma variável com um número é inválido:

| Válido | Inválido |
| :--- | :--- |
| WORD | 1WORD |
| \_WORD | WORD-WORD |
| WORD\_WORD |  |
| WORD10 |  |

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

## Argumentos

Pode-se acessar os argumentos de um script através de um conjunto de variáveis especiais:

- $0: armazena o nome do script
- $1: armazena o valor do primeiro argumento
- $2 ... $x: armazena o valor do argumento x.
- $@: Todos os argumentos separados por espaços

Existe, então, uma variável que contêm o valor de cada posição de argumento indicado.


## Estruturas de controle

### Estruturas condicionais

Temos à disposição a estrutura condicional básica `if` em shell script, que é escrita como:

``` bash
if [[ EXPRESSION ]]; then; 
    # code
elif [[ EXPRESSION ]]; then
    # code
else
    # code
fi

```

Para executar validação booleana, utiliza-se o comando `test`. Na estrutura de código acima, o `[[` é um shorthand para este comando. Existem duas variações: `[ ]` e `[[ ]]`: este segundo é chamado new test e é suportado nos terminais bash, z e korm. Para saber como construir expressões de teste, execute `man test`. As principais são:

- `-z STRING`: valida se o tamanho de uma String é zero
- `INTEGER -eq INTEGER`: valida se dois inteiros são iguais (há também -ne, -gt, -lt).

Quando usa-se o new test `[[`, é possível usar `&&` e `||` para representar `AND` e `OR`.


### Estruturas de repetição

Temos à disposição as estruturas `for` e `while`. Um `for` possui estrutura que permite que uma "lista" seja processada de forma iterativa ("lista", porque se tratar de diversas Strings separadas por espaços).

``` bash
for ARG in ${@}; do
    echo -e "\t- ${ARG}"
done
```

Utilizando esta estrutura básica é possível iterar pela lista de argumentos passados ao Script. Um `while` recebe uma expressão condicional da mesma forma que um `if`:

``` bash
while [[ "$VARIABLE" = 'true' ]]; do
    # code
done
```







