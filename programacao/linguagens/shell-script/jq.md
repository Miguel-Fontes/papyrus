---
description: Um processador de Json de linha de comando
---

# jq

O [jq](https://stedolan.github.io/jq/), um processador de Json de linha de comando. É a principal ferramenta utlizada para trabalhar com JSONs em um script SH \(julgando pela quantidade de recomendações\).

A ferramenta possui uma grande quantidade de funcionalidades úteis para trabalhar com Json, e aqui irei listar snippets úteis.

## Snippets

### Formatando um json

Podemos usar o jq para formatar um Json, indicando que ele utilize o filter identidade '.'.

```bash
$ echo '{"foo":  "bar"}' | jq '.'
{
  "foo": "bar"
}
```

Esta pode ser uma funcionalidade bastante útil no dia a dia, particularmente quando usada junto do [xclip](https://github.com/astrand/xclip). Copie qualquer json não formatado com `ctrl + c`, e então execute o comando abaixo para formatá-lo. Considerando que o json `{"foo":  "bar"}` está copiado em seu clipboard:

```bash
# Exibe no terminal o Json formatado
$ xclip -out | jq '.'
{
  "foo": "bar"
}

# Formata o Json e o coloca novamente no seu clipboard. Cole-o onde desejar.
$ xclip -out | jq '.' | xclip -selection clipboard
```

### Selecionando atributos

Para selecionar um atributo específico de um Json, usamos um seletor json similar ao do Json Path.

```bash
$ echo '{"foo":  "bar"}' | jq '.foo'
"bar"
```

Para selecionar itens em outros níveis de um Json, basta indicar o "caminho" pelos atributos seguindo este mesmo dot notation.

```bash
$ echo '{"foo": "bar", "object": {"key": "value"}}' | jq '.object.key'
"value"
```

### Selecionando items de arrays

Arrays? Suportados!

```bash
echo '[{"foo":  "bar"}, {"object": {"key": "value"}}]' | jq '.[1]'
{
  "object": {
    "key": "value"
  }
}
```

### Verificando se uma chave existe

Podemos identificar se um item existe verificando se o valor retornado pelo jq foi 'null', pois este é o valor retornado se a chave não for localizada.

```bash
$ echo '{"foo":  "bar"}' | jq '.bar'
null
```

Uma segunda forma \(e que prefiro\) é utilizando o filtro `has(key)`.

```bash
$ echo '{"foo":  "bar"}' | jq 'has("something")'
false

$ echo '{"foo":  "bar"}' | jq 'has("foo")'
true

$ echo '{"foo":  "bar", "object": {"key": "value"}}' | jq 'has("object")'
true
```

Com isto, podemos validar se o valor retornado é `true` ou `false`. Um detalhe sobre o `has` é que ele sempre busca a chave indicada no nível topo do Json, ou seja, não seria possível validar `jq 'has("object.key")'`. Para executar esta validação, podemos utilizar um pipe \(veja em "Usando pipes"\).

### Usando pipes

Como fazemos no shell, é possível combinar filtros com pipes. 

```bash
$ echo '{"foo":  "bar", "object": {"key": "value"}}' | jq '.object | has("key")'
true
```

### Obtendo todas as chaves e valores de um json

Para automatizar processos muitas vezes precisamos ler todas as chaves de um arquivo Json de forma dinâmica. O jq suporta isso com o filtro `keys`.

```bash
$ echo '{"foo": "bar", "object": {"key": "value"}}' | jq 'keys'
[
  "foo",
  "object"
]
```

### Valide mais de uma condição \(usando AND\)

Para verificar se duas chaves específicas existem, podemos usar o operador AND.

```bash
# Não use este password em casa.
$ echo '{"user": "user", "password": "pwd"}' | jq 'has("user") and has("password")'
true

# Se ambas as chaves não existirem, false será retornado.
echo '{"user": "user"}' | jq 'has("user") and has("password")'
false
```

Da mesma forma, são suportados os operadores `or` e `not`.

```bash
$ echo '{"user": "user"}' | jq 'has("user") or has("password")'
true

$ echo '{"user": "user"}' | jq 'has("user") and has("password") | not'
true
```

### Operador de alternativa

Quer indicar um valor default se uma chave não for encontrada ou um valor for avaliado como falso? É possível com o operador de alternativa.

```bash
# Esta busca é avaliada para 'false', portanto o retorno é substituído por "nothing".
$ echo '{"foo":  "bar"}' | jq 'has("something") // "nothing"'
"nothing"

# O mesmo ocorre para um seletor que retornaria 'null'.
$ echo '{"foo":  "bar"}' | jq '.something // 1'
1

# Se a chave existir, o valor permanece inalterado.
$ echo '{"foo":  "bar"}' | jq '.foo // "nothing"'
"bar"
```

### if-then-else

E se fosse possível validar o json com expressões condicionais e executar ações ou retornar outros valores diretamente no filtro. É possível. Considere o seguinte exemplo, onde lê-se um Json com o status de uma compra. A intenção é alterar o valore retornado para uma String amigável:

```bash
declare -r PAID="Pago"
declare -r PENDING="Pendente"
declare -r CANCELED="Cancelada"

STATUS=$(echo '{"status":  "PAID"}' | jq -r "if .status == \"PAID\" then \"$PAID\"
                                  elif .status == \"PENDING\" then \"$PENDING\"
                                  elif .status == \"CANCELED\" then \"$CANCELED\"
                                  else .status
                                  end")
```

Esta funcionalidade é particularmente interessante quando combinada com a possibilidade de criar filtros \(veja em Crie seus próprios filtros\).

### Defina seus próprios filtros

Criar funções é interessante localmente, mas e se pudéssemos isolar estas definições e utilizá-las quando desejado, podendo ser inclusive em mais de um script? You got it!

```bash
declare -r PAID="Pago"
declare -r PENDING="Pendente"
declare -r CANCELED="Cancelada"

jqFmtStatus="def jqFmtStatus(\$status): if status == \"PAID\" then \"$PAID\"
                                        elif status == \"PENDING\" then \"$PENDING\"
                                        elif status == \"CANCELED\" then \"$CANCELED\"
                                        else status
                                        end"
                                        
STATUS=$(echo '{"status":  "PAID"}' | jq -r "$jqFmtStatus; jqFmtStatus(.status)")
```

### Extraia o schema de um json

Para extrair o schema de um Json, use `jq -r '.[0] | path(..) | map(tostring) | join("/")'`\(créditos da ideia para [jq: JSON - Like a Boss](https://pt.slideshare.net/btiernay/jq-json-like-a-boss), e os dados do JSON para [JsonPlaceHolder](https://jsonplaceholder.typicode.com/)\).

```bash
$ curl -sS https://jsonplaceholder.typicode.com/users | jq -r '.[0] | path(..) | map(tostring) | join("/")'

id
name
username
email
address
address/street
address/suite
address/city
address/zipcode
address/geo
address/geo/lat
address/geo/lng
phone
website
company
company/name
company/catchPhrase
company/bs
```

### Saída raw

Ao usar as opções `--raw-output / -r` qualquer saída do jq será escrita diretamente no standard output, ao invés de ser formatada como uma Sring json.

```bash
$ echo '{"foo": "bar"}' | jq '.foo'
"bar"

$ echo '{"foo": "bar"}' | jq -r '.foo'
bar
```

Geralmente, quando vamos utilizar o valor em outras operações em scripts bash, é esta segunda forma que desejamos.

### Iterando por atributos/items

É possível iterar sobre atributos/items de um Json. Para tal, podemos usar a seguinte estrutura `while`.

```bash
echo '{"foo": "value", "bar": "value2"}' | jq -rc '.[]' | while IFS='' read ATTR; do
    echo "$ATTR"
done

# Ouputs:
# value1
# value2

echo '[{"foo":"value"}, {"bar":"value"}]' | jq -rc '.[]' | while IFS='' read ATTR; do
    echo "$ATTR"
done

# Outputs:
# {"foo":"value"}
# {"bar":"value"}
```

## Referências e links externos

* [jq](https://stedolan.github.io/jq/), um processador de JSON leve e flexível na linha de comando. É a principal ferramenta utlizada para trabalhar com JSONs em um script SH.
* [xclip](https://github.com/astrand/xclip), uma interface de linha de comando ao clipboard X11.
* [jq: JSON - Like a boss](https://pt.slideshare.net/btiernay/jq-json-like-a-boss), uma apresentação bastante interessante sobre o jq. Recomendo. 
* [JsonPlaceHolder](https://jsonplaceholder.typicode.com/), provê apis com dados falsos facilitando testes e prototipação.

## Relacionado

* [Acessando APIs Rest](acessando-apis-rest.md)

