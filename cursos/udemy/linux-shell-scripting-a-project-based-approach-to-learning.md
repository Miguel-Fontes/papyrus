---
description: Aprenda Shell Script fazendo.
---

# Linux Shell Scripting: A Project-Based Approach to Learning

## Introdução

O curso Linux [Shell Scripting: A Project-Based Approach to Learning](https://www.udemy.com/linux-shell-scripting-projects/learn/v4/overview) promete ensinar Shell Script fazendo: cada etapa do curso representa um projeto ou parte de um projeto. Parece uma didática interessante principalmente para quem já tem uma noção do básico da sintaxe.

## Seção 1 e 2

* Um overview do curso e configuração de ambiente utilizando Vagrant. A ideia de utilizar o [Vagrant](https://www.vagrantup.com/) é, provavelmente, para evitar que um aluno não consiga executar os exercícios por sua distribuição e/ou terminal não possuir algum recurso utilizado nas aulas. 
* Vou pular, uma vez que tenho o [Docker](https://www.docker.com/) à disposição. Estou executando um container com um volume sincronizado. Utilizo o comando abaixo para criar um container debian, e conectar via bash:

```bash
docker run --rm -it  \
  -v /home/miguel/temp/sh-env:/temp/sh-env \
  --entrypoint /bin/bash \
  debian:buster
```

* Neste comando:
  * --rm: remove o container automaticamente quando ele desligar, o que irá acontecer assim que `exit` for invocado para desligar a conexão ssh ao bash.
  * -it: abreviado para `--interactive --tty`,  o que garante uma experiência no shell como se fosse o meu próprio host.
  * -v: define o diretório compartilhado com o container, utilizando um Docker Volume \(diretório\_host:diretório\_container\).
  * --entrypoint: O comando que será executado quando o container iniciar-se.
  * debian:buster: imagem buster, do linux debian.
* Ao executar este comando, a conexão com o shell do container será feita. Ao navegar ao diretório `/home/miguel/temp/sh-env`, os arquivos estarão disponíveis.

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

* O clássico comando `chmod` altera as permissões de arquivo, e recebe como argumento um numero criptico \(`chmod 755`\). Este número, é claro, significa os acessos que estamos definindo no arquivo, e é construído mapeando os caracteres `rwx` ao números `r = 4; w = 2; x = 1`. Considerando estes valores:
  * 7   = 4 + 2 + 1 = r+w+x
  * 5   = 4 + 1     = r + x
  * 755 = rwxr-xr-x
* A grande força do bash é poder utilizar todas os programas de linha de comando que temos disponíveis no Linux.
* Variáveis podem começar com underlines ou uma letra. Iniciar o nome de uma variável com um número é inválido:

| Válido | Inválido |
| :--- | :--- |
| WORD | 1WORD |
| \_WORD | WORD-WORD |
| WORD\_WORD |  |
| WORD10 |  |

* Outro conceito importante é que usar aspas duplas ao redor de varáveis `"` faz com que o valor de uma variável seja expandido, enquanto aspas simples `'` não.
  * `echo "$WORD"`: exibirá o  valor da variável `WORD`.
  * `echo '$WORD'`: imprimirá '$WORD'.

### Aula 12

* A expressão utilizada em `if` em shell scripts varia, e algumas destas formas não são intuitivas à um iniciante. 
* Uma forma de avaliação lógica muito usada, depende do uso de brackets `[]` como em `if [[ "${UID}" -eq 0 ]]; then... else`... `fi`. 
* Existem duas formas possíveis:
  * Single bracket: quando escrevemos o a expressão condicional no `if` com um único bracket estamos, na realidade, utilizando o comando `test` \[[8](https://www.shellscript.sh/test.html)\] para avaliar a expressão lógica.
  * Double bracket: esta forma utiliza o novo comando `test` que possui diversas melhorias, mas não é compátivel com qualquer shell, apenas o Bash\[[5](https://pt.wikipedia.org/wiki/Bash)\], Z shell\[[6](https://en.wikipedia.org/wiki/Z_shell)\], e Korn\[[7](https://pt.wikipedia.org/wiki/Korn_Shell)\].
* Se não houver a necessidade de usar o comando `test`, os brackets não devem ser escritos.
* Ao executar `type -a [` podemos ver que `[` é um comando e um built-in do shell!
* Execute o comando `man test` para ver as validações suportadas.
* Concluo que devo utilizar `[[` sempre, a não ser que o Script deva ser altamente portável.
* **Atenção**: os espaços entre a expressão lógica e os brackets devem existir.
* As principais diferenças entre `[` e `[[` estão listadas nas duas tablas a seguir, e foram obtidas da página _What is the difference between test, \[ and \[\[?_ \[[4](http://mywiki.wooledge.org/BashFAQ/031)\]:

| **Feature** | **new test** \[\[ | **old test** \[ | **Example** |
| :--- | :--- | :--- | :--- |
| string comparison | &gt; | \&gt; \(\*\) | \[\[ a &gt; b \]\] \|\| echo "a does not come after b" |
|  | &lt; | \&lt; \(\*\) | \[\[ az &lt; za \]\] && echo "az comes before za" |
|  | = \(or ==\) | = | \[\[ a = a \]\] && echo "a equals a" |
|  | != | != | \[\[ a != b \]\] && echo "a is not equal to b" |
| integer comparison | -gt | -gt | \[\[ 5 -gt 10 \]\] \|\| echo "5 is not bigger than 10" |
|  | -lt | -lt | \[\[ 8 -lt 9 \]\] && echo "8 is less than 9" |
|  | -ge | -ge | \[\[ 3 -ge 3 \]\] && echo "3 is greater than or equal to 3" |
|  | -le | -le | \[\[ 3 -le 8 \]\] && echo "3 is less than or equal to 8" |
|  | -eq | -eq | \[\[ 5 -eq 05 \]\] && echo "5 equals 05" |
|  | -ne | -ne | \[\[ 6 -ne 20 \]\] && echo "6 is not equal to 20" |
| conditional evaluation | && | -a \(\*\*\) | \[\[ -n $var && -f $var \]\] && echo "$var is a file" |
|  | \|\| | -o \(\*\*\) | \[\[ -b $var \|\| -c $var \]\] && echo "$var is a device" |
| expression grouping | \(...\) | \\( ... \\) \(\*\*\) | \[\[ $var = img\* && \($var = \*.png \|\| $var = \*.jpg\) \]\] && echo "$var starts with img and ends with .jpg or .png" |
| Pattern matching | = \(or ==\) | \(not available\) | \[\[ $name = a\* \]\] \|\| echo "name does not start with an 'a': $name" |
| RegularExpression matching | =~ | \(not available\) | \[\[ $\(date\) =~ ^Fri\ ...\ 13 \]\] && echo "It's Friday the 13th!" |

* \(\*\) Uma extensão ao padrão POSIX, alguns shells podem suportá-los, outros não.
* \(\*\*\) Os operadores -a e -o, e o agrupamento \(...\) são definidos nos padrões POSIX mas apenas para alguns casos específicos e estão marcados como deprecated. O uso destes operadores é desencorajado, use múltiplos comandos `[` ao invés disto:
  * if \[ "$a" = a \] && \[ "$b" = b \]; then ...
  * if \[ "$a" = a \] \|\| { \[ "$b" = b \] && \[ "$c" = c \];}; then ...
* Existem também algumas expressões primitivas que `[[` foi feito para suportar, enquanto a implementação de `[` pode não as possuir.

| **Description** | **Primitive** | **Example** |
| :--- | :--- | :--- |
| entry \(file or directory\) exists | -e | \[\[ -e $config \]\] && echo "config file exists: $config" |
| file is newer/older than other file | -nt / -ot | \[\[ $file0 -nt $file1 \]\] && echo "$file0 is newer than $file1" |
| two files are the same | -ef | \[\[ $input -ef $output \]\] && { echo "will not overwrite input file: $input"; exit 1; }  |
| negation | ! | \[\[ ! -u $file \]\] && echo "$file is not a setuid file" |

### Aula 13

* O comando `exit` encerra a execução de um script. Este comando recebe um código de status ou, caso este seja omitido, o status da execução do comando anterior será utilizado.
* A sintaxe `$(command)` captura o resultado do comando, permitindo que este seja armazenado em uma variável ou usado como argumento para outros comandos.
* A variável `?` armazena o código de saída do último comando, portanto, podemos testar o status da execução com a condicional `if [[ "${?}" -ne 0 ]]; then; echo "Error"; fi;`.
* Para testar se duas strings são iguais, usa-se o operador `eq`, como em: `[[ "${x}" -eq "${y}" ]]`.
* Para testar se duas strings são diferentes, usa-se o operador `eq`, como em: `[[ "${x}" -ne "${y}" ]]`.

### Aula 14

* As principais formas de ler input do usuário são:
  * Argumentos
  * O comando `read`.
  * Variáveis de ambientes
* Existem 3 tipos padrão de input e output:
  * Standard input
  * Standard output
  * Standard error
* O comando `read` pode ser utilizado com a chave `-p` para pedir um valor específico ao usuário e salva-lo em uma variável. A estrutura é `read -p 'prompt-string' VARIABLE`. Para exemplificar, o script abaixo irá imprimir a opção digitada pela usuário:

```bash
#!/bin/bash
read -p 'Deseja prosseguir(Y/n): ' PROCEED
echo "$PROCEED"
```

* O comando `useradd` adiciona um novo usuário.
* O comando `passwd` pode alterar a senha de um usuário.
* O comando `echo` pode ser usado para enviar dados para um segundo comando utilizando-se de um pipe \(`|`\). Um exemplo disto é a passagem de um novo password para o comando `passwd` em `echo "$PASSWORD" | passwd -stdin`.
* Para  fazer com que um usuário tenha que alterar a senha em seu primeiro login, usa-se a chave `-e` do comando  `passwd`, que irá expirar a senha atual \(`passwd -e "${USER_NAME}"`\).

### Aula 15 e 16

* É um exercício de criação de um script para criação de usuários. O roteiro é o seguinte:
  * Chama-se `add-local-user.sh`.
  * Deve ser executado como um superuser, e caso contrário ele deve sair com um status 1.
  * Questiona o nome do novo usuário, o nome da pessoa, e o password inicial.
  * Cria um novo usuário no sistema.
  * Informa ao usuário se a conta não pode ser criada por algum motivo. Se a conta não foi criada, o script deve sair com o status 1.
  * Exibe o nome do usuário, senha e host onde a conta foi criada.

```bash
#!/bin/bash

declare LOGIN=""
declare USER_NAME=""
declare PASSWORD=""

guardIsSuperUser() {
  if [[ "${UID}" -ne 0 ]]; then
    echo "ERROR: Current user is not root! Please, try again with sudo or as root"
    exit 1
  fi
}

promptForArguments() {
  read -p 'User login name: ' LOGIN
  read -p 'Person or application name that will use this account: ' USER_NAME
  read -p 'Initial password: ' PASSWORD
}

createNewUser() {
  useradd -c "${USER_NAME}" -m "${LOGIN}"

  if [[ "${?}" -ne 0 ]]; then
    echo "ERROR: There was an error on the user creation process! Please, review the given arguments and try again!"
    exit 1
  fi
}

setDefaultPassword() {
  echo "${LOGIN}:${PASSWORD}" | chpasswd
  passwd -e "${LOGIN}"
}

showNewUserInformation() {
  echo "..."
  echo "User [${LOGIN}] created successfully! The user will be prompted to change his password on first login."
  echo "Login: ${LOGIN}"
  echo "User name: ${USER_NAME}"
  echo "Initial password: ${PASSWORD}"
  echo "Hostname: ${HOSTNAME}"

}

main() {
  guardIsSuperUser
  promptForArguments
  createNewUser
  setDefaultPassword
  showNewUserInformation
  exit 0
}

main
```

## Seção 4

Próximos capítulos

## Referências e links

1. Docker:[ https://www.docker.com/](https://www.docker.com/)
2. Permissões de arquivo: [https://help.ubuntu.com/community/FilePermissions](https://help.ubuntu.com/community/FilePermissions)
3. Stack Overflow - What is the difference between double and single square brackets in bash?: [https://serverfault.com/questions/52034/what-is-the-difference-between-double-and-single-square-brackets-in-bash](https://serverfault.com/questions/52034/what-is-the-difference-between-double-and-single-square-brackets-in-bash)
4. What is the difference between test, \[ and \[\[ ?: [http://mywiki.wooledge.org/BashFAQ/031](http://mywiki.wooledge.org/BashFAQ/031)
5. Bash shell: [https://pt.wikipedia.org/wiki/Bash](https://pt.wikipedia.org/wiki/Bash)
6. Korn shell: [https://pt.wikipedia.org/wiki/Korn\_Shell](https://pt.wikipedia.org/wiki/Korn_Shell)
7. Z shell: [https://en.wikipedia.org/wiki/Z\_shell](https://en.wikipedia.org/wiki/Z_shell)
8. Comando test: [https://www.shellscript.sh/test.html](https://www.shellscript.sh/test.html)
9. 
