---
description: >-
  Como o shell expande valores, e funcionalidades interessante para geração de
  dados.
---

# Expansões

## Expansão de chaves \(curly brackets\)

Podemos usar esta sintaxe para gerar uma lista de valores útil para diversos fins.

```bash
echo {a..z}
a b c d e f g h i j k l m n o p q r s t u v w x y z

echo {A..Z}
A B C D E F G H I J K L M N O P Q R S T U V W X Y Z

$ echo {1..20}
1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17 18 19 20
```

É possível usar a expansão de chaves para iterarmos sobre um conjunto de números ou caracteres por exemplo.

{% code-tabs %}
{% code-tabs-item title="numeros.sh" %}
```bash
# Imprime os números de 1 ~ 10
for i in {1..10}; do 
    echo $i 
done

```
{% endcode-tabs-item %}

{% code-tabs-item title="caracteres.sh" %}
```bash
# Imprime os caracteres de a ~ z. Também é possível usar A..Z.
for i in {a..z}; do 
    echo $i 
done
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Note que este feature na realidade gera todos os caracteres entre os dois que são indicados, portanto, usar `{a..Z}` resulta em algo talvez inesperado.

```bash
$ echo {a..Z}
a ` _ ^ ] \ [ Z
```

É possível também aninhar expansões.

```bash
$ echo {{1..3},{a..c}}
1 2 3 a b c
```

Quando alinhamos duas expansões lado a lado, esta é feita recursivamente, gerando todas as combinações possíveis. Este comportamento é similar à função `zip`.

```bash
$ echo {1..3}{a..c}
1a 1b 1c 2a 2b 2c 3a 3b 3c
```

E também é possível definir um _step_ de incremento.

```bash
$ echo {00..50..5}
00 05 10 15 20 25 30 35 40 45 50
```

## Expansão aritmética

É possível expressões aritméticas nativamente no shell, usando a expansão aritmética representada pela sintaxe `$((expression))`.

```bash
echo $(( 3 + 4 * 5 / 2 - 1))
12
```

## Substituição de comando

É a substituição de um comando por seu valor, feito através da sintaxe `$(expressão)`.

```bash
$  echo "Novo uuid: $(uuidgen | awk '{print tolower($1)}')"
Novo uuid: bd2f2bb6-9385-4fb5-8d06-9050e05acb3e
```

No exemplo acima os comandos indicados em `$(expressão)` foram substituídos por seu output, resultando na construção correta da String. Esta funcionalidade é muito poderosa, possibilitando a composição de diversos scripts.

## Substituição de processo

É importante citar este recurso já que ele pode nos ajudar na criação de Scripts que utilizem  A substituição de processo é um recurso similar ao `subshell` e algumas vezes também é citado como sendo um _pipe reverso._

A substituição de processo é feita usando a sintaxe `<(comando)` ou `>(comando)` . Esta funcionalidade permite que o input ou o output do _comando_ dentro da estrutura seja referenciado como um arquivo \(`/dev/fd/<n>`\),

```bash
$ echo <(true)
/dev/fd/12

$ echo >(true)
/dev/fd/13
```

Retirado do manual \(`man sh`, pesquise `Process Substitution`.\):

> If the &gt;\(list\) form is used, writing to the file will provide input for list.  If the &lt;\(list\) form is used, the file passed as an argument should be read to obtain the output of list.

Este recurso é útil para quando não podemos utilizar pipes: quando precisando passar mais de um argumento ou o script espera um arquivo, por exemplo. Um caso interessante é o comando `diff`. Este comando espera dois arquivos para executar a comparação e, por isso, podemos usar a substituição de processo `<()`.

```bash
$ diff -u <(sort -n <<< $VALUES_1) <(sort -n <<< $VALUES_2)
$ diff -u <(ls dir1) <(ls dir2)
```

O motivo deste recurso ser chamado de _pipe reverso_ é porque podemos utilizar ele em alguns casos em que o pipe poderia ser usado. Mas em casos como esse, considero o uso do Pipe mais legível devido à direção da leitura dos comandos ser exatamente a ordem em que serão executados e ter forma similar à composição de funções.

```bash
$ ls | cat -n  # ls     (1) -> cat -n (2)
$ cat -n <(ls) # cat -n (2) -> ls     (1)
```

Pode-se combinar também _Process Substitution_ com _Input Redirection._ No exemplo abaixo, o output de date é obtido e armazenado em um arquivo `/dev/fd/12`, que é lido pelo operador de direcionamento `<` e passado para o comando `wc`.

```bash
$ wc -l < <(date)
       1
```

Já a substituição de processo na direção de input `>()` tem casos de uso mais complexos, e não consegui encontrar exemplos úteis para o meu dia a dia. Neste formato, nós conectamos o _stdin_ dos comandos dentro da estrutura com o conteúdo de um arquivo `/dev/fd/<n>`.

```bash
$ echo "1234567890" > >(awk '{print length}')
10
```

Usamos novamente o input redirection para ilustrar como este formato funciona. No exemplo acima, direcionamos o output de `echo "1234567890` usando `>`para `/dev/fd/<n>`, que foi lido pelo comando `awk '{print length}'`. É claro que este exemplo é apenas para fins de ilustração, já que poderia ser reescrito como:

```bash
$ echo "1234567890" | awk '{print length}'
10
```

## Word Splitting

O shell automaticamente quebra as palavras em diferentes itens dos resultados das expansões de comando, aritmética e parâmetro. O separador usado nestas quebras é o valor da variável `IFS` \(internal field separator\), que é configurada automaticamente pelo shell.

Podemos manipular o valor desta variável para produzir resultados diferentes das substituições citadas acima, assim como para ler dados em um formato diferente. O valor padrão da variável IFS é `IFS=$' \t\n'`.

## Referências e links

* [Gnu.org Process Substitution](http://www.gnu.org/software/bash/manual/html_node/Process-Substitution.html#Process-Substitution)

