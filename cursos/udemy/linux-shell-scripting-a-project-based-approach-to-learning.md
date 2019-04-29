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
* Uma forma de avaliação lógica muito usada, depende do uso de brackets `[]` como em `if [[ "${UID}" -eq 0 ]]; then... else... fi`. 
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

### Aula 17

* Para obtermos um número aleatório no bash, precisamos apenas obter o valor da variável de ambiente `${RANDOM}`.
* No Bash\[[5](https://pt.wikipedia.org/wiki/Bash)\], podemos usar o caractere `!` para acessar os designadores de evento\[[9](https://linuxacademy.com/blog/linux/event-designators-bash-basics/)\]. Um exemplo, é executar `!v`: este comando irá reexecutar o último comando iniciando em `v`\(`vim ./file.sh` seria um match, por exemplo\).
* O Linux tem uma representação de data chamada Era Linux\[[10](https://pt.wikipedia.org/wiki/Era_Unix)\] que ficou conhecida como Unix Epoch ou apenas Epoch. Esta representação de data é basicamente um inteiro indicando a quantidade de segundos que se passaram desde 01/01/1970 00:00:00 \(UTC\).
* Um checksum\[[12](https://pt.wikipedia.org/wiki/Soma_de_verifica%C3%A7%C3%A3o)\] é um valor numérico gerado para um bloco de dados, que e relativamente único. 
  * É muito usado para validar se um arquivo transferido não foi corrompido durante o processo, onde comparamos um checksum gerado localmente com o publicado pela parte que disponibilizou o arquivo.
  * Existem vários algoritmos de geração de checksums disponíveis\[[12](https://pt.wikipedia.org/wiki/Soma_de_verifica%C3%A7%C3%A3o)\]: 
    * cksum
    * md5sum
    * sha1sum
    * sha224sum
    * sha256sum
    * sha384sum
    * sha512sum
    * sum
* Uma forma bastante básica mas funcional de construir passwords pseudo aleatórios é com o comando `date +%s%N | sha256sum | head -c32`. 
  * date +%s%N: a data no formato Epoch \(%s\) junto dos nanosegundos \(%N\)
  * sha256sum: transforma o input em um checksum sha256
  * head -c32: obtem os primeiros 32 caracteres do input.
* O comando `fold` executa um wrap em cada linha recebida como input para que ela tenha um tamanho especificado.
* O comando `shuf` imprime uma permutação aleatória dos dados recebidos como input.

```bash
$ echo "abc" | fold -w1
a
b
c

$ echo "abc" | fold -w1 | shuf
c
a
b
```

### Aula 18

* Argumentos são os valores passados para um programa / script.
* Estes valores são acessados no script através dos parâmetros posicionais: um conjunto de variáveis numeradas que indicam a posição do argumento. Para acessá-los:`${1}`, `${2}`, `${3}` e assim por diante.
* Na posição $`{0}` estará armazenado o caminho completo para o script. Este valor é bastante usado junto do comando `basename` para obter apenas o nome do script. Esta combinação é muito útil ao criar textos de ajuda.

```bash
#!/bin/bash

declare -r DEVICE_ID=$1
declare -r USAGE="
USAGE: $(basename $0) <DEVICE_ID>
       $(basename $0) 15
DEVICE_ID
  The device id, identifiable through 'xinput list'
"

help() {
  echo "$USAGE"
}
```

* O comando `hash` é um built-in do linux usado para armazenar o local dos programas executados recentemente, criando uma espécie de [cache](https://pt.wikipedia.org/wiki/Cache). É possível limpar esta tabela executando`hash -r`.
* O parâmetro `${#}` armazena a quantidade de argumentos passados ao programa.
* O manual do bash, acessível através de `man bash` possui muita informação importante para a escrita de scripts.
* Da mesma forma, é possível acessar o manual de diversos comandos como o `for` uma vez que, no universo shell script, tudo é um programa e não um keyword como em uma linguagem de programação. Experimente executar `man for`.
* A sintaxe do for loop em shell é bastante similar, mas possui suas nuances. A estrutura padrão é listada abaixo.

```bash
#!/bin/bash

for X in 1 2 3 4 5; do
  echo "${X}"
done
```

* O valores deverão ser expandidos para que o `for` funcione corretamente.
* Para criar um for que execute uma vez para cada argumento recebido, use `${@}`. Este parâmetro possui a lista de argumentos passada na execução \(ver seção _special parameters_ do manual do bash\).

### Aula 19

* Podemos usar o loop na forma `while`. É útil, como sempre, quando queremos repetir a execução enquanto um predicado manter-se verdadeiro.

```bash
#!/bin/bash

X=1
while [[ "${X}" -eq 1 ]]; do
  echo "X = ${X}"
  X=7
done
```

* O comando `shift n` altera os paràmetros posicionais, descartando uma quantidade n da esquerda.  Se nenhum argumento for informado ao comando `shift`, o default 1 é assumido.
  * No caso da execução de `shift 1` ou `shift`, o valor de `${1}` é descartado, e passa a ser o valor de `${2}`.

{% code-tabs %}
{% code-tabs-item title="shift-test.sh" %}
```bash
#!/bin/bash

while [[ "$#" -gt 0 ]]; do
  echo "> $@"
  shift
done

# Outputs...
# $ ./shift-test.sh 1 2 3 4 5
# > 1 2 3 4 5
# > 2 3 4 5
# > 3 4 5
# > 4 5
# > 5
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Aula 20 e 21

* Outro exercício onde tenho que escrever um script para criação de usuários.
  * O arquivo deve ser chamado _add-new-local-user.sh_
  * Garante que será executado com privilégios de superuser \(root\). Encerra com status 1 caso negativo.
  * Exibe uma mensagem similar à que seria exibida em uma página man se o usuário não informar o nome da conta, encerrando a execução com sstatus 1.
  * Usa o primeiro argumento como o nome para a conta. Qualquer outro\(s\) argumento\(s\) serão usados como comentários da conta.
  * Gera uma nova senha automaticamente para a conta.
  * Informa ao usuário se houve erro na criação por algum motivo. Neste caso, encerra com status 1.
  * Exibe o nome de usuário, senha e host em que a conta foi criada.
* Extendendo no que havia criado no exercício anterior, cheguei na solução abaixo. Foram poucas alterações, na realidade. 
* Note o uso da forma `${@:2}` para buscar todos os parametros após o primeiro. Trata-se de um [range](https://wiki.bash-hackers.org/scripting/posparams#range_of_positional_parameters), onde estamos indicando que faremos a expansão de todos os parâmetros à partir do segundo \(`${@:INICIO}`\).

{% code-tabs %}
{% code-tabs-item title="add-new-local-user.sh" %}
```bash
#!/bin/bash

declare LOGIN=$1
declare COMMENTS="${@:2}"
declare PASSWORD=$(date +%s%N | sha256sum | head -c32)
declare -r USAGE="
NAME
    $(basename $0) - creates a new user

SYNOPSIS
    $(basename $0) [LOGIN] [COMMENTS]...
    $(basename $0) user \"New application user\"
    $(basename $0) user \"New application user\" \"Uses foo application\"

DESCRIPTION
    Creates a new user on the host, with a given LOGIN name and COMMENTs that describe who is the user and what kind of access it uses.

    Exit status:
        0   if OK,
        1   if an error has ocurred.

AUTHOR
    Written by Miguel Fontes, for the Udemy Linux Shell Scripting: A Project-Based Approach to Learning course.

SEE ALSO
    Course page at <https://www.udemy.com/linux-shell-scripting-projects/learn/v4>
"
guardValidParameters() {
  if [[ -z "$LOGIN" ]]; then
    echo "ERROR: Mandatory LOGIN argument was not given!"
    echo "${USAGE}"
    exit 1
  fi
}

guardIsSuperUser() {
  if [[ "${UID}" -ne 0 ]]; then
    echo "ERROR: Current user is not root! Please, try again with sudo or as root"
    exit 1
  fi
}

createNewUser() {
  useradd -c "${COMMENTS}" -m "${LOGIN}"

  if [[ "${?}" -ne 0 ]]; then
    echo "ERROR: There was an error on the user creation process! Please, review the given arguments and try again!"
    exit 1
  fi
}

setDefaultPassword() {
  PASSWORD=$(date +%s%N | sha256sum | head -c32)
  echo "${LOGIN}:${PASSWORD}" | chpasswd
  passwd -e "${LOGIN}"
}

showNewUserInformation() {
  echo "..."
  echo "User [${LOGIN}] created successfully! The user will be prompted to change his password on first login."
  echo "Login: ${LOGIN}"
  echo "Initial password: ${PASSWORD}"
  echo "Hostname: ${HOSTNAME}"
}

main() {
  guardValidParameters
  guardIsSuperUser
  createNewUser
  setDefaultPassword
  showNewUserInformation
  exit 0
}

main
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Seção 5

### Aula 22

* É possível redirecionar o standard input e standard output entre arquivos e programas. Isto é feito com os operadores `>` e `<`.

```bash
$ echo "secret" > password
$ cat password
secret
$ sudo paasswd --stdin einstein < password
Changing password for user einstein
```

* Note que os operadores `>` e `<` sobrescrevem os conteúdos de um arquivo, quando usados desta forma. Caso este não seja o comportamento desejado, use os operadores de append `>>` e `<<`.

```bash
$ echo "secret" > password
$ echo "abc,123" > password
$ cat password
abc,123
$ echo "secret" > password
$ echo "abc,123" >> password
secret
abc,123
```



### Aula 23

Ainda não!

## Referências

1. Docker:[ https://www.docker.com/](https://www.docker.com/)
2. Permissões de arquivo: [https://help.ubuntu.com/community/FilePermissions](https://help.ubuntu.com/community/FilePermissions)
3. Stack Overflow - What is the difference between double and single square brackets in bash?: [https://serverfault.com/questions/52034/what-is-the-difference-between-double-and-single-square-brackets-in-bash](https://serverfault.com/questions/52034/what-is-the-difference-between-double-and-single-square-brackets-in-bash)
4. What is the difference between test, \[ and \[\[ ?: [http://mywiki.wooledge.org/BashFAQ/031](http://mywiki.wooledge.org/BashFAQ/031)
5. Bash shell: [https://pt.wikipedia.org/wiki/Bash](https://pt.wikipedia.org/wiki/Bash)
6. Korn shell: [https://pt.wikipedia.org/wiki/Korn\_Shell](https://pt.wikipedia.org/wiki/Korn_Shell)
7. Z shell: [https://en.wikipedia.org/wiki/Z\_shell](https://en.wikipedia.org/wiki/Z_shell)
8. Comando test: [https://www.shellscript.sh/test.html](https://www.shellscript.sh/test.html)
9. Bash event designators basics: [https://linuxacademy.com/blog/linux/event-designators-bash-basics/](https://linuxacademy.com/blog/linux/event-designators-bash-basics/)
10. Era Linux: [https://pt.wikipedia.org/wiki/Era\_Unix](https://pt.wikipedia.org/wiki/Era_Unix)
11. GNU Bash Event Designators: [https://www.gnu.org/software/bash/manual/html\_node/Event-Designators.html](https://www.gnu.org/software/bash/manual/html_node/Event-Designators.html)
12. Checksum: [https://pt.wikipedia.org/wiki/Soma\_de\_verifica%C3%A7%C3%A3o](https://pt.wikipedia.org/wiki/Soma_de_verifica%C3%A7%C3%A3o)
13. Shell Scripting Tutorial - For loops: [https://www.shellscript.sh/loops.html](https://www.shellscript.sh/loops.html)
14. Bash Hackers Wiki - Arrays: [https://wiki.bash-hackers.org/syntax/arrays](https://wiki.bash-hackers.org/syntax/arrays)
15. Bash Hackers Wiki - Range Of Positional Parameters: [https://wiki.bash-hackers.org/scripting/posparams\#range\_of\_positional\_parameters](https://wiki.bash-hackers.org/scripting/posparams#range_of_positional_parameters)

## Relacionados

* [Shell Script](../../programacao/linguagens/shell-script.md)

