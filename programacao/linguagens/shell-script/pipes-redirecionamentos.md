---
description: 'Compondo processos com scripts, comandos, e arquivos!'
---

# Pipes e redirecionamentos

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

## Referências e links

* [JsonPlaceholder](https://jsonplaceholder.typicode.com)

