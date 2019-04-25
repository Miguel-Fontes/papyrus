---
description: Aprenda Shell Script fazendo.
---

# Linux Shell Scripting: A Project-Based Approach to Learning

## Introdução

O curso Linux [Shell Scripting: A Project-Based Approach to Learning](https://www.udemy.com/linux-shell-scripting-projects/learn/v4/overview) promete ensinar Shell Script fazendo: cada etapa do curso representa um projeto ou parte de um projeto. Parece uma didática interessante principalmente para quem já tem uma noção do básico da sintaxe.

## Seção 1 e 2

Um overview do curso e configuração de ambiente utilizando Vagrant. A ideia de utilizar o [Vagrant](https://www.vagrantup.com/) é, provavelmente, para evitar que um aluno não consiga executar os exercícios por sua distribuição e/ou terminal não possuir algum recurso utilizado nas aulas. Vou pular, uma vez que tenho o [Docker](https://www.docker.com/) à disposição.

## Seção 3

### Aula 11

* Explicações básicas sobre o uso do Vagrant
* A primeira linha de um shell script é o famoso shebang `#!/bin/bash`, que define qual o interpretador dos comandos presentes no arquivo.
* Quando executamos `ls -l` em um diretório, as [permissões de arquivo](https://help.ubuntu.com/community/FilePermissions) são exibidas no terminal no formato `drwxrwxrwx` onde:
  * Um caractere `d` ou `-`indicando se o arquivo é um diretório.
  * E 3 blocos `rwx` indicando respectivamente acesso de leitura, escrita e execução para o dono do arquivo, membros do grupo do arquivo e para todos. Novamente, se o usuário, grupo ou todos não possuírem acesso, a posição será exibida como `-`.
  * Um output exemplo:

```bash
➜  papyrus git:(master) ls -l
total 16
drwxrwxr-x 2 miguel miguel 4096 abr 23 20:57 pesquisa-and-redacao
drwxrwxr-x 3 miguel miguel 4096 abr 22 08:41 programacao
-rw-rw-r-- 1 miguel miguel 1540 abr 22 08:41 README.md
-rw-rw-r-- 1 miguel miguel 1173 abr 24 19:55 SUMMARY.md
```

* O clássico comando `chmod` altera as permissões de arquivo, e recebe como argumento um numero criptico (`chmod 755`). Este número, é claro, significa os acessos que estamos definindo no arquivo, e é construído mapeando os caracteres `rwx` ao números `r = 4; w = 2; x = 1`. Considerando estes valores:
    * 7   = 4 + 2 + 1 = r+w+x
    * 5   = 4 + 1     = r + x
    * 755 = rwxr-xr-x
* A grande força do bash é poder utilizar todas os programas de linha de comando que temos disponíveis no Linux.
* Variáveis podem começar com underlines ou uma letra. Iniciar o nome de uma variável com um número é inválido:

| Válido    | Inválido  |
|-----------|-----------|
|  WORD     | 1WORD     |
| _WORD     | WORD-WORD |
| WORD_WORD |           |
| WORD10    |           |

* Outro conceito importante é que usar aspas duplas ao redor de varáveis `"` faz com que o valor de uma variável seja expandido, enquanto aspas simples `'` não.
  * `echo "$WORD"`: exibirá o  valor da variável `WORD`.
  * `echo '$WORD'`: imprimirá '$WORD'.

## Referências e links

* Docker:[ https://www.docker.com/](https://www.docker.com/)
* Permissões de arquivo: [https://help.ubuntu.com/community/FilePermissions](https://help.ubuntu.com/community/FilePermissions)

