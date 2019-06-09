---
description: >-
  Como criar pequenas funções para atividades pontuais sem ter de criar um
  arquivo .sh
---

# Scripts on-demand

Algumas vezes, o que precisamos é de uma pequena funcionalidade para uso pontual - não justificando a criação de um arquivo sh. Podemos fazer isso no shell? Certamente!

Listarei aqui como fazê-lo e práticas úteis para trabalhar desta forma com no shell.

## Criando funções na linha de comando

Por algum motivo demorei para considerar essa possibilidade, mesmo sabendo que um shell script é somente uma sequência de comandos que você poderia executar manualmente na linha de comando, só que armazenadas em um arquivo.

Para criar novas funções diretamente na linha de comando, declare-a da mesma forma que você faria em um arquivo. Para exemplificar: suponha que há a necessidade de renomear um arquivo com um prefixo "DONE" para indicar que algum tipo de processamento tenha sido feito sobre ele.

```bash
$ markAsDone() { if [[ -e "$1" ]]; then; echo "ERROR: No file name was given" >&2; fi; mv $1 $(dirname $1)/DONE-$(basename $1); }
```

Executada esta instrução, podemos usar o comando `which markAsDone` para visualizar como esta foi criada e está disponível para uso!

```bash
$ which markAsDone
markAsDone () {
        if [[ -e "$1" ]]
        then
                echo "ERROR: No file name was given" >&2
        fi
        mv $1 $(dirname $1)/DONE-$(basename $1)
}
```

Não há diferença entre esta função e uma que estaria escrita em um arquivo. Sempre que precisar executar uma tarefa muito pontual repetidas vezes, é possível usar este recurso à seu favor.

Para 'remover' esta função use `unset -f markAsDone`, ou inicie outra sessão.

## Usando source

Outro recurso interessante é que podemos usar o `source` à nossa favor. Qualquer função contida em scripts ficará disponível na linha de comando se você invocar o comando `source` sobre ele.

Considere que temos um arquivo com a mesma função anterior:

{% code-tabs %}
{% code-tabs-item title="markAsDone.sh" %}
```bash
#!/bin/bash

markAsDone () {
	if [[ -e "$1" ]]
	then
		echo "ERROR: No file name was given" >&2
	fi
	mv $1 $(dirname $1)/DONE-$(basename $1)
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Sempre que precisarmos desta função disponível podemos usar o comando `source`:

```bash
$ source ./markAsDone.sh
$ markAsComplete fileName.txt
```

Este é um recurso muito interessante para reusarmos funções de arquivos que funcionam como 'bibliotecas'.

## Iterando sobre valores

Uma necessidade comum é iterar sobre valores e executar alguma operação. Pode-se usar o `for` diretamente para isto! Considere o eseguinte arquivo csv de exemplo:

```text
date,customer,product,value
2019-05-01,44ca1341-b129-4ead-91b6-cda4d7db1743,e0a8550f-59c2-48cb-88c3-ccf3ef5c13e7,100
2019-05-06,0aacdf97-e650-4840-868e-e1fa9b47499f,16e191f6-786d-4092-8261-0837526a8b27,200
2019-05-20,f1966322-1096-475c-b6a2-00f28a94e040,2c7f07e7-8b86-47d8-8665-e2416062b6df,150
```

Desejamos saber qual o total em vendas, o valor contido no campo 4. Poderíamos fazer o seguinte:

```bash
$ VALUES=$(cat sales.csv| cut -d ',' -f 4 | grep -E "[0-9]" | paste -s -d ' ')
$ TOTAL=0; for VALUE in $(echo $VALUES); do TOTAL=$(echo "$TOTAL + $VALUE" | bc); done; echo $TOTAL
450
```

Dois detalhes importantes aqui: o `paste -s -d ' '` irá transformar a lista de valores de uma lista em 'linhas' para uma lista separada por espaços. Para garantir o funcionamento correto do `for..in`, usamos o artifício do `echo` no value desta variável.

## Use variávels com estado intermediário

O exemplo anterior utilizou uma variável chamada `VALUES` com a lista a ser iterada. Use esta técnica sempre que possível.

Armazenar valores intermediários é essencial para construir construir instruções diretamente na linha de comando, sem se confundir com o está fazendo. E o melhor, é fácil. Abuse.





