---
description: Aprenda Shell Script fazendo.
---

# Linux Shell Scripting: A Project-Based Approach to Learning

## Introdução

O curso Linux [Shell Scripting: A Project-Based Approach to Learning](https://www.udemy.com/linux-shell-scripting-projects/learn/v4/overview) promete ensinar Shell Script fazendo: cada etapa do curso representa um projeto ou parte de um projeto. Parece uma didática interessante principalmente para quem já tem uma noção do básico da sintaxe.

## Seção 1 e 2

Um overview do curso e configuração de ambiente utilizando Vagrant. A ideia de utilizar o [Vagrant](https://www.vagrantup.com/) é, provavelmente, para evitar que um aluno não consiga executar os exercícios por sua distribuição e/ou terminal não possuir algum recurso utilizado nas aulas. Vou pular, uma vez que tenho o [Docker](https://www.docker.com/) à disposição.

## Seção 3

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

* 
## Referências e links

* Docker:[ https://www.docker.com/](https://www.docker.com/)
* Ubuntu File permissions: [https://help.ubuntu.com/community/FilePermissions](https://help.ubuntu.com/community/FilePermissions)

