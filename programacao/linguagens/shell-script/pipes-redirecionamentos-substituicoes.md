---
description: As formas de comunicação entre processos e arquivos disponíveis no Shell.
---

# Pipes, redirecionamentos, e substituições

De longe, um dos recursos mais interessantes do terminal é o pipe \(`|`\) e todas as possibilidades interessantes que ele permite: passar um valor por uma série de transformações sequenciais, até chegar no que deseja. Junto das funcionalidades de redirecionamento e substituição, conseguimos compor processos com scripts, comandos, e arquivos.

## Pipes

Pipes permitem que o output de um comando seja passado como input para um próximo comando \(passamos o _stdout_ de um script, para o _stdin_ do próximo\). Veja abaixo um exemplo do uso do comando `uuidgen` e `awk` para gerar um novo UUID com todos os caracteres em minúsculas.

```bash
$ uuidgen | awk '{print tolower($0)}'
c66b40b1-0939-4544-9eb2-09d1d01fe314
```

Em grande parte, o potencial do shell deve-se ao pipe. A facilidade de conexão de diferentes processos cria possibilidades muito interessantes ao escrever pequenas instruções pontuais ou grandes scripts.

```bash
curl -sS "https://jsonplaceholder.typicode.com/todos" \
  | jq -r 'map(select(.id >= 100 and .id <= 150) | .title) | join("\n")' \
  | cat -n
```

O exemplo acima: 

* Acessa a lista de todos exemplo do [JsonPlaceholder](https://jsonplaceholder.typicode.com/todos) \(teste abaixo o request, se quiser ver o payload retornado, ou acesse o link\).
* O `jq` é usado para filtrar apenas os com ids entre 100 e 150 \(incluindo o 100 e 150\) retornando uma lista de seus títulos separadas por quebra de linha.
* Usa-se o `cat -n` para numerar a lista títulos dos items identificados.

## Redirecionamentos

Redirecionamento é a ação de abrir um arquivo e escrever o seu conteúdo no _stdin_ de um processo. É uma funcionalidade útil para que possamos usar o conteúdo de arquivos com comandos que entendem apenas de _streams_ como o `tr`.

```bash
$ cat file | tr 'o' 'b'
fbb

$ tr 'o' 'b' < file
fbb
```

Outro uso de redireção é quando desejamos escrever a saída de um comando em um arquivo.

```bash
$ echo "1234567890" > file.txt
$ echo "abcdefgh" >> file.txt
$ cat file.txt
1234567890
abcdefgh
```

Ao usar `>` um novo arquivo é criado, sobrescrevendo qualquer um já existente. Utilizando de `>>` um novo arquivo é criado se necessário, caso contrário, o conteúdo será concatenado \(append\) ao final dele.

## Substituição de processo

É importante citar este recurso já que ele pode nos ajudar na criação de Scripts que utilizem pipes de forma pesada. A substituição de processo é um recurso similar ao `subshell` e algumas vezes também é citado como sendo um _pipe reverso._

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

## Referências e links

* [Gnu.org Process Substitution](http://www.gnu.org/software/bash/manual/html_node/Process-Substitution.html#Process-Substitution)
* [JsonPlaceholder](https://jsonplaceholder.typicode.com)

