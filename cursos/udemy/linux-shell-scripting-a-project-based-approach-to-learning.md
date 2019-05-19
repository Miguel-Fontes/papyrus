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
* Para testar se dois inteiros são iguais, usa-se o operador `eq`, como em: `[[ "${x}" -eq "${y}" ]]`.
* Para testar se dois inteiros são diferentes, usa-se o operador `ne`, como em: `[[ "${x}" -ne "${y}" ]]`.
* Da mesma forma, pode-se testar igualdade e desigualdade de strings com `=` e `!=`.

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

* É possível redirecionar o standard input e standard output entre arquivos e programas. Isto é feito com os operadores de redireção `>` e `<`.

```bash
$ echo "secret" > password
$ cat password
secret
$ sudo paasswd --stdin einstein < password
Changing password for user einstein
```

* Note que os operadores `>` e `<` sobrescrevem o conteúdo de um arquivo, quando usados desta forma. Caso este não seja o comportamento desejado, use os operadores de append `>>` e `<<`.

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

* Um descritor de arquivo é um número que referencia um arquivo aberto.
  * FD 0 - STDIN
  * FD 1 - STDOUT
  * FD 2 - STDERR
* O Linux representa praticamente tudo como um arquivo: seu teclado, que é o STDIN padrão é representado como um arquivo. 
* O interessante disto é que é possível redirecionar o output de comandos, que normalmente seriam enviados para sua tela, para um arquivo ou outro comando, por exemplo.
* Se você quiser redirecionar um valor para o STDIN é possível escrever `read X 0< ~/.tmux.conf`. 
* Quando executamos uma redireção de input sem um descritor, o 0 é usado como default e é por isso que `read X < ~/.tmux.conf` funciona da mesma forma que o exemplo com `0<`.
* Para redirecionar o output, usamos como antes o operador `>`. O comando `echo "${UID}" > uid` irá salvar o UID em um arquivo chamado uid. 
* O comando `echo "${UID} 1> uid` funcionará exatamente da mesma forma, pois em um output o descritor 1 é o padrão.
* Com o STDERR podemos visualiza este comportamento mais explicitamente. Quando redirecionamos o output de um comando `head`, por exemplo.

```bash
$ head -n1 /arquivo-inexistente 1> head.out
head: cannot open 'arquivo-inexistente' for reading: No such file or directory
```

* No exemplo acima tentamos redirecionar a saída do head para um arquivo, mas como o comando retornou um erro, a mensagem foi impressa no terminal pois um erro não é m STDOUT.
* Para redirecionar um erro, usamos o descritor 2.

```bash
$ head -n1 /arquivo-inexistente 2> head.err
$ cat head.err
head: cannot open 'arquivo-inexistente' for reading: No such file or directory
```

* Como tempos essa separação de descritores podemos, por exemplo, redirecionar o output para um arquivo e os erros para um segundo.

```bash
$ head -n1 /arquivo-inexistente >> head.out 2>> head.err
$ cat head.err
head: cannot open 'arquivo-inexistente' for reading: No such file or directory
```

* Uma última forma possível seria gravar ambas as saídas em um mesmo arquivo, o que é possível com a terceira forma `2>&1`.

```bash
$ head -n1 /arquivo-inexistente >> head.both 2>&1
$ cat head.both
head: cannot open 'arquivo-inexistente' for reading: No such file or directory
```

* A forma `2>&1` faz com que o STDERR\(2\) seja redirecionado para o STDOUT\(1\). Como o STDOUT está sendo redirecionado para o arquivo head.both, ambas as saídas são concatenadas no mesmo aquivo.
* O caractere `&` faz com que o `1` seja tratado como um descritor. Ao escrever `2>1`, este caractere `1` será tratado como um arquivo.
* Este redirecionamento dos outputs de saída e erro para um arquivo é tão comum que existe um "atalho": `&>` e `&>>`. 

```bash
$ head -n1 /arquivo-inexistente &> head.both
$ cat head.both
head: cannot open 'arquivo-inexistente' for reading: No such file or directory
```

* Um ponto interessante é que quando usamos pipes \(`|`\) o STDERR não é passado como argumento para os comandõs seguintes. Note como no comando abaixo, o erro de leitura do primeiro `cat` não foi  passado para o seguinte.

```bash
$ cat /etc/environment /arquivo-inexistente | cat -n
cat: /arquivo-inexistente: No such file or directory
     1  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
```

* Podemos usar o redirecionamento para solucionar isso. Podemos, no primeiro comando incluir o `2>&1`ou usar a forma especial do pipe `|&`.

```bash
$ cat /etc/environment /arquivo-inexistente |& cat -n
     1  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
     2  cat: /arquivo-inexistente: No such file or directory

$ cat /etc/environment /arquivo-inexistente 2>&1 | cat -n
     1  PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games"
     2  cat: /arquivo-inexistente: No such file or directory
```

* É uma prática muito comum redirecionar outputs para `/dev/null` quando desejamos descartá-los. Isto é porque muitas vezes precisamos apenas saber se um comando foi bem sucedido e, para isto, podemos utilizar a variável `${?}`.

```bash
$ head -n3 /inexistent-file &> /dev/null
```

### Aula 24 e 25

* Exercício que dá continuidade ao script de criação de usuários.
  * Nomeado _add-newer-local-user.sh._
  * Garante que será executado com privilégios de superuser \(root\). Encerra com status 1 caso negativo. Todas as mensagens associadas com esse evento deverão ser exibidas no standard error.
  * Exibe uma mensagem similar à que seria exibida em uma página man se o usuário não informar o nome da conta, encerrando a execução com status 1. Todas as mensagens associadas com esse evento deverão ser exibidas no standard error.
  * Usa o primeiro argumento como o nome para a conta. Qualquer outro\(s\) argumento\(s\) serão usados como comentários da conta.
  * Gera uma nova senha automaticamente para a conta.
  * Informa ao usuário se houve erro na criação por algum motivo. Neste caso, encerra com status 1. Todas as mensagens associadas com esse evento deverão ser exibidas no standard error.
  * Exibe o nome de usuário, senha e host em que a conta foi criada.
  * Suprime o output de todos os outros comandos.
* Ainda utilizando o mesmo script como exemplo, a solução foi adicionar `>&2` nos pontos onde os echos deveriam ser enviados ao STDERR, e `&> /dev/null` onde todo output deveria ser suprimido.

{% code-tabs %}
{% code-tabs-item title="add-newer-local-user.sh" %}
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
    echo "ERROR: Mandatory LOGIN argument was not given!" >&2
    echo "${USAGE}" >&2
    exit 1
  fi
}

guardIsSuperUser() {
  if [[ "${UID}" -ne 0 ]]; then
    echo "ERROR: Current user is not root! Please, try again with sudo or as root" >&2
    exit 1
  fi
}

createNewUser() {
  useradd -c "${COMMENTS}" -m "${LOGIN}"

  if [[ "${?}" -ne 0 ]]; then
    echo "ERROR: There was an error on the user creation process! Please, review the given arguments and try again!" >&2
    exit 1
  fi
}

setDefaultPassword() {
  PASSWORD=$(date +%s%N | sha256sum | head -c32)
  echo "${LOGIN}:${PASSWORD}" | chpasswd
  passwd -e "${LOGIN}" &> /dev/null
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

## Seção 6

### Aula 26

* Quando temos código ou consideramos construir uma lista de instruções `if`, pode ser que estejamos em uma situação onde o `case` resolveria melhor a situação.

```bash
case "${!} in
    start)
        echo "Starting."
        ;;
    stop)
        echo "Stopping."
        ;;
    status|state|--status|--state)
        echo "Status."
        ;;
    *)
        echo "Not supported!."
        ;;
esac
```

* O último case `*)` significa "match anything", agindo como nosso `else`. O _pattern_ usado no case aceita qualquer padrão suportado no _pattern matching_ padrão do bash \(ver em `man bash`, pesquisar por _P_a_ttern Matching_\).
* É possível tambem usar o pipe \(`|`\) para usar mais e um padrão por caso. Entenda o pipe como um `OR`.

### Aula 27

* Funções são definidas com a sintaxe `nome() { ... }`.
* Podem ser invocadas como um comando padão `nome arg1 arg2 arg3`.
* Uma função deve ser definida antes de ser usada.

```bash
#!/bin/bash

function() {
    echo "You called the log function!"
}

log
```

* Uma função tem acesso ao escopo global de variáveis, mas seu conjunto de argumentos \(`$1`, `$2`, ...\) é local. 
* O fato da função ter acesso ao escopo global significa que podemos ter problemas de conflitos de nomes. Para evitar, podemos usar a keyword `local`.

```bash
#!/bin/bash

function() {
    local MESSAGE="${@}"
    echo "${MESSAGE}"
}

log "Hello!"
```

* A palavra chave `readonly` transforma uma variável em uma constante. Um exemplo é `readonly VERBOSE=true`.
* O comando `logger` nos concede acesso às funcionalidades de log de sistema do Linux. É possível prover diversas informações, mas as principais são um tag e uma mensagem: `logger -t "my-script" "my message"`.
* Uma forma comum de usar o logger em um script é `logger -t $0 "message"` . Nesta forma, o tag será sempre o nome do arquivo de script em que o log foi gravado.
* Para ler o log: `sudo tail /var/log/messages`.
* Em funções, utilize `return` ao invés de `exit`. O return irá apenas encerrar a função, retornando um código de status numérico, enquanto `exit` irá encerrar o script, o que geralmente não é o comportamento adequado.

### Aula 28

* O comando `getopts` nos permite facilmente tratar opções de linha de comando.
* O comando `getopts` recebe dois argumentos: 1\) a definição das opções e 2\) a variável que receberá a opção atual. Este coando é um built-in do bash, e é suportado diretamente pelo `while`.

```bash
while getopts ab:cde OPCAO; do
   case "${OPCAO}" in
      a) recebi_a=1 ;;
      b) argumento_b="${OPTARG}" ;;
      c) recebi_c=1 ;;
      d) recebi_d=1 ;;
      e) recebi_e=1 ;;
   esac
done
```

* A definição das opções do `getopts` funciona da seguinte forma:
  * Cada caractere indicado torna-se uma chave `-x` onde x é o nome da opção indicado na sequência.
  * Se o caractere da opção possuir um `:` à sua direita, significa que ele recebe um valor como argumento, e isto será validado pelo `getopts`. 
* Portanto, na definição `ab:cde`, estamos definindo as opções: `-a`, `-b [ARG]`, `-c`,  `-d`, `-e`.
* Estes argumentos geralmente são tratados em uma cláusula `case`, conforme listado acima.

```bash
#!/bin/bash

LENGTH=48

usage() {
    echo "Usage: $0 [-vs] [l LENGTH]" >&2
    echo 'Generate a random password.'
    echo '  -l LENGTH   Specify the password length'
    echo '  -s          Append a special character to the password.'
    echo '  -v          Increase verbosity.'
    exit 1
}

log() {
    local MESSAGE="${@}"
    if [[ "${VERBOSE}" = 'true' ]]; then
        echo "$MESSAGE"
    fi
}

while getopts vl:s OPTION; do
    case ${OPTION} in
        v)
            VERBOSE='true'
            echo 'Verbose mode on.'
            ;;
        l)
            LENGTH="${OPTARG}"
            ;;
        s)  
            USE_SPECIAL_CHARACTER='true'
            ;;
        ?)
            usage
            ;;
    esac
done

log 'Generating a password.'

PASSWORD=$(date +%s%N${RANDOM}${RANDOM} | sha256sum | head -c${LENGTH})

if [[ "${USE_SPECIAL_CHARACTER}" = 'true' ]]; then 
    log 'Selecting a random special character'
    SPECIAL_CHARACTER=$(echo '!@#$%&*()-=+' | fold -w1 | shuf | head -c1)
    PASSWORD="${PASSWORD}${SPECIAL_CHARACTER}"
fi 

log 'Done.'
log 'Here is the password: '

echo "${PASSWORD}"

exit 0
```

* Neste exemplo, o último match `?` foi usado para indicar qualquer caractere.

### Aula 29

* Para executar operações artiméticas, deve-se utilizar a sintaxe `NUM=$(( 2 * 4 ))`.
* **Importante**: o bash não suporta operações com ponto flutuante. 
  * Para tal use o `bc` da seguinte forma: `echo '6 / 4' | bc -l`.
  * Outra opção é utilizar `awk`, `awk 'BEGIN {print 6/4}'`.
* O bash suporta os operadores `++` e `--`,  `(( NUM++ ))`.
* Além disso, é possível utilizar operadores como `+=` e `-=`.
* O comando `let` permite a execução de operações aritméticas `let NUM='2 + 3'`. Execute `help let | less` para saber sobre os operadores suportados.
* `expr` é outro comando que permite a execução de operações, `NUM=$(expr 1 + 1)`.
* Estes cálculos aritméticos nos apoiam à ampliar nosso uso do `getopts`. O `getopts` processa todas as opções listadas, no entanto, não limpa a lista de argumentos ao fim, impossibilitando o acesso fácil à qualquer argumento adicional. Para fazê-lo, podemos utilizar o `shift` e uma simples fórmula.
* O `getopts` armazena em uma variável chamada `OPTIND` a posição do 'próximo argumento'. Ao final do processamento das opções, esta variável terá o valor do primeiro argumento nao proessado \(o primeiro argumento restante\).
* Podemos implementar a regra então como `shift "$(( OPTIND -1 ))"`, o que irá fazer um shift até o primeiro argumento não consumido `getopts`.

### Aula 30

* Podemos procurar comandos usando o comando locate, `locate userdel`.
* O comando `locate` usa o índice do linux que é atualizado uma vez por dia. Caso um comando não seja localizado, use `sudo updatedb` para atualizar os índices.
* Outro comando para procurar comandos é o find, utilizando `find /usr/bin -name [`.

### Aula 31

* Podemos usar o comando `userdel` para remover uma conta de usuário.
* Ao executar `sudo userdel user1` o usuário `user1` será deletado. Usando a chave `-r`, o diretório home deste usuário e deletado no processo.
* Podemos ver o id de uma conta de usuário com o comando `id -u user`.
* No arquivo `/etc/login.defs` define-se os ids automáticos gerados para os usuários do sistema. Por default, contas de sistemas recebam UIDs entre 201 e 999, facilitando sua identificação.

### Aula 32

* Podemos criar um arquivo `tar` com `tar -cvf archive.tar mydir/`.
* Podemos extrair com `tar -xf ../archive.tar`. Este comando extrai o arquivo em um diretório do nível superior.
* Podemos compactar um arquivo com `gzip` usando `gzip archive.tar`.

### Aula 33

* O comando `chage` nos permite alterar a data de expiração de uma conta: `sudo chage -E 0 user1`.
* Podemos também "trancar" um usuário com o comando `passwd -l user1`, e destrancar com `passwd -u user1`.

### Aula 34 e 35

* Exercício onde um script para deleção de contas deverá ser desenvolvido.
* Os requisitos são:
  * Deve chamar-se `disable-local-user.sh`.
  * Garante que é executado com acesso root. Caso contrário encerra a execução com status 1, imprimindo mensagem de erro no standard error.
  * Exibe mensagem similar à uma página _man_ se o usuário não informar um nome de conta, encerrando com status 1 e imprimindo no standard error.
  * Desabilita \(expira e trava\) contas por padrão.
  * Possibilita as seguintes opções:
    * -d: deleta a conta ao invés de desabilitar.
    * -r: remove o diretório HOME associado à conta.
    * -a: cria um arquivo com o diretório HOME associado à conta, e o armazena no diretório `/archives`, criando o diretório caso ele não exista.
    * Qualquer outra opção fará com que o script exiba a mensagem de uso, saindo com o status 1.
  * Aceita uma lista de nomes de usuário como argumento. Ao menos um nome de usuário deve ser provido, caso contrário deve-se exibir a mensagem de uso, encerrando com status 1 e imprimindo para o standard error.
  * Não permite que contas com UID menor que 1000 sejam desabilitadas ou deletadas.
  * Informa o usuário se agum erro ocorreu ao desabilitar, deletar ou arquivar.
  * Exibe o nome do usuário e todas as ações executadas na conta.

```bash
#!/bin/bash

# Disable local user
#
# Disables or delete a local user, providing options to delete, and store a archive of the user HOME directory.

declare -r USAGE="
Usage: $(basename $0) [-d] [-r] [-a] <USER>...
Disables / deletes user(s).                                                    

    -d  Deletes the user instead of disabling.                                 
    -r  Deletes the HOME directory associated with the given user.             
    -a  Creates a archive of the HOME directory of the given user on /archives.
"

declare -r TRUE='true'
declare -r FALSE='false'

declare -r DEFAULT="\033[0m"
declare -r GREEN="\033[0;32m"
declare -r RED="\033[0;31m"

declare VERVOSE="$FALSE"
declare DELETE="$FALSE"
declare REMOVE="$FALSE"
declare ARCHIVE="$FALSE"

usage() {
    echo -e "$USAGE" >&2
    exit 1
}

log() {
    local MESSAGE="${@}"
    if [[ "$VERBOSE" == "$TRUE" ]]; then
        echo "$MESSAGE"
    fi
}

out() {
    local TYPE=$1
    local MESSAGE=$2

    if [[ "$TYPE" == "ERROR" ]]; then
        echo "${RED}${MESSAGE}${DEFAULT}"
    elif [[ "$TYPE" == "SUCCESS" ]]; then
        echo "${GREEN}${MESSAGE}${DEFAULT}"
    else
        echo "$MESSAGE"
    fi

    return 0
}

lock() {
    local USER="$1"
    echo -n "Locking user [$USER]... "

    passwd -l "$USER" &>/dev/null
    if [[ "${?}" -ne 0 ]]; then
        out "ERROR" "Error!"
        echo "Could not lock user [$USER]!"
        return 1
    fi

    out "SUCCESS" "Success!"
    return 0
}

disable() {
    local USER="$1"
    echo -n "Disabling user [$USER]... "

    chage -E 0 "$USER" &>/dev/null
    if [[ "${?}" -ne 0 ]]; then
        out "ERROR" "Error!"
        echo "Could not disable user [$USER]!"
        return 1
    fi

    out "SUCCESS" "Success!"
    return 0
}

archive() {
    local USER="$1"
    echo -n "Archiving user [$USER] home diretory to /archives as ${USER}.tar... "

    if [[ ! -d "/archives" ]]; then
        mkdir /archives
    fi

    tar -cvf "/archives/${USER}.tar" "/home/$USER/" &>/dev/null
    if [[ "${?}" -ne 0 ]]; then
        out "ERROR" "Error!"
        echo "Could not archive user [$USER] home directory!"
        return 1
    fi

    out "SUCCESS" "Success!"
    return 0
}

delete() {
    local USER="$1"
    echo -n "Deleting user [$USER]... "

    userdel "$USER" &>/dev/null
    if [[ "${?}" -ne 0 ]]; then
        out "ERROR" "Error!"
        echo "Could not delete user [$USER] with reason [$?]!"
        return 1
    fi

    out "SUCCESS" "Success!"

    return 0
}

removeHome() {
    local USER="$1"
    echo -n "Deleting user [$USER] home directory... "

    rm -rf "/home/$USER" &>/dev/null
    if [[ "${?}" -ne 0 ]]; then
        out "ERROR" "Error!"
        echo "Could not remove user [$USER] home directory!"
        return 1
    fi

    out "SUCCESS" "Success!"
    return 0

}

if [[ "${UID}" -ne 0 ]]; then
    echo "ERROR: This script requires super user (root) privileges!" >&2
    exit 1
fi

while getopts drav OPTION; do
    case ${OPTION} in
    d)
        DELETE="$TRUE"
        ;;
    r)
        REMOVE="$TRUE"
        ;;
    a)
        ARCHIVE="$TRUE"
        ;;
    v)
        VERBOSE="$TRUE"
        echo "Verbose mode on."
        ;;
    ?)
        usage
        ;;
    esac
done

# Prints the execution parameters
log "Execution parameters:"
log "    - Delete account?        $DELETE"
log "    - Remove Home directory? $REMOVE"
log "    - Archive a backup?      $ARCHIVE"
echo ""

# Shifts to the remaining arguments
shift "$((OPTIND - 1))"

# Validates the user account argument
declare -r USERS="${@}"

if [[ -z "$USERS" ]]; then
    echo "ERROR: At least one (1) user must be given!" >&2
    usage
fi

for USER in $USERS; do
    echo "Processing user [$USER]... "

    USER_ID=$(id -u $USER 2> /dev/null)

    # Validate if user exists
    if [[ -z "$USER_ID" ]]; then
        echo "ERROR: User [$USER] does not exists!"
        exit 1
    fi

    # Validates if is a administrative user
    if [[ "$USER_ID" -lt 1000 ]]; then
        echo "ERROR: Deleting or disabling the administrative user [$USER] is not allowed!" >&2
        exit 1
    fi

    # Archive home directory if requested
    if [[ "$ARCHIVE" == "$TRUE" ]]; then
        archive "$USER"
    fi

    # Removes the home directory if requested
    if [[ "$REMOVE" == "$TRUE" ]]; then
        removeHome "$USER"
    fi

    # Deletes a user, or lock and disable.
    if [[ "$DELETE" == "$TRUE" ]]; then
        delete "$USER"
    else
        lock "$USER"
        disable "$USER"
    fi

done

exit 0

```

## Seção 7

### Aula 36

* O comando `cut` imprime partes específicas de linhas de arquivos informados via standard input.
* Para obter o caractere n de cada linha de um arquivo, use a opção `-c` . Executar `cut -c 1 /etc/psswd` irá imprimir o primeiro caractere de cada linha do arquivo passwd.
* Para obter um intervalo de caracteres, utilize um intervalo com a opção `-c`. Executar `cut -c 4-7 /etc/passwd` irá imprimir os caracteres 4 - 7 de cada linha do arquivo.
* Para obter todos os caracteres à partir de um índice específico, use um intervalo aberto: `cut -c 4- /etc/passwd`.
* Para obter todos os caracteres até um índice, inverta o intervalo aberto usado anteriormente: `cut -c -4 /etc/passwd`.
* Para obter uma sequência de índices, separe-os com vírgula como em `cut -c 1,5,8 /etc/passwd`.
* A chave `-b` imprimir caracteres por byte.: `cut -b 1 /etc/passwd`.
* A chave `-f` permite que "campos" sejam obtidos, o que é útil para processar arquivos CSV, por exemplo. Use `echo 'one,two,three' | cut -d ',' -f 3` para obter "three". O delimitador padrão é `\t`.
* A saida do comando `cut`, quando usadá a chave `-f` utiliza o mesmo delimitador. Para alterá-lo de separado por vírgulas para separado por tabulação podemos utilizar a chave `--output-delimiter='\t'`.
* O comando `grep` aceita expressões regulares, e possui uma chave `-v` que é um not, retornando todas as linhas de m arquivo que não foram um match da expressão indicada.
* Podemos usar o awk para executar operações complexas. Considere que desejamos obter o valor do campo 2 da string `DATA:firstDATA:last`. Se usássemos `cut -d 'DATA:' -f 2 <file>`, receberíamos um erro, pois uma limitação do `cut` é que um delimitador precisa ter um único caractere. O awk não tem esta limitação: `awk -F 'DATA:' '{print $2}' <file>`.
* Neste comando, a instrução entre `{}` é um programa `awk`. 
* Podemos acessar diferentes campos com `awk -F ':' '{print $1, $3}' /etc/passwd`.
* O separador padrão do `awk` é um espaço. Para removê-lo omita o espaço entre os campos:  `awk -F ':' '{print $1,$3}' /etc/passwd`.
* Para alterar o separador para outro caractere use o parâmetro `-v OFS=','` antes da definição do programa.
* Outra opção, é definir uma String personalizada no programa: `awk -F ':' '{print $1 ", " $3}' /etc/passwd`.
* O `awk` possui uma variável `$NF` que conhece a quantidade de campos de cada linha.
* É possível executar operações artiméticas usando a sintaxe `$( $NF - 1 )` em um programa `awk`. 
* O separador de campos padrão do `awk` é espaço em branco.

### Aula 37

* Podemos obter todas as portas abertas com `netstat -nutl | grep ':' | awk '{print $4}' | awk -F ':' '{print $NF}'` .

### Aula 38

* O comando `sort` ordena o seu input alfabeticamente. Exemplo: `sort /etc/passwd`.
* A chave `-r` ordena na ordem inversa: `sort -r /etc passwd`.
* O comando `sort` não ordena números corretamente se você não utilizar a chave `-n`.  Este comportamento pode ser exemplificado com o comando `cut -d ':' -f 3 /etc/passdw | sort -n`.
* O comando `sort` possui uma opção que formata números que estejam em formato legível \(human readable form\). Um exemplo disto pode ser visto combinando-o com `du`: `sudo du -h /var | sort -h`.
* A chaecho "1 119.15.137.149" \| cut -f 1ve `-u` do comando `sort` remove duplicatas em um conjunto de dados.
* Existe um comando chamado `uniq` que faz exatamente o mesmo trabalho da chave `-u` do comando `sort`. O único detalhe deste comando é que ele só funciona com dados ordenados.
* Usando a chave `-c` do comando `uniq` uma contagem da quantidade de vezes que um valor se repetiu será exibida. Exemplificando: `sudo cat /var/log/messages | awk '{print $5}' | sort | uniq -c`.
* O comando `wc` possibilita a contagem de linhas, palavras, e caracteres. Execute `wc /etc/passwd` para visualizar uma listagem de número de linhas, número de palavras, número de caracteres.
* Se quiser saber apenas a quantidade de linhas, use a chave `-l` do comando `wc`.
* Para executar um `sort` por um campo específico, use a opção `-k`. No comando `cat /etc/passwd | sort -t ':' -k 3 -n`, executa-se um sort numérico no campo 3, do arquivo passwd. O campos são delimitados pelo separador ':' indicado em `-t`.
* O comando `tail -3` imprime as três últimas linhas de uma arquivo.

### Aula 39 e 40

* Criação de um script para leitura de logs que atenda aos seguintes requisitos:
  * Nomeado _show-attackers.sh_.
  * O script requer que ao menos um arquivo seja indicado como argumento. Se o arquivo não for informado ou não puder ser lido, uma mensagem de erro deve ser impressa e a execução deve ser encerrada com status 1.
  * Conta a quantidade de tentativas login mal sucedidas por IP. Se existirem ips com mais de 10 tentativas mal sucedidas, o número de tentativas, o endereço IP e a localização do IP deverão ser exibidas.
  * Use o geoiplookup para encontrar a localização do ip \(em distribuições debian, instale com `sudo apt-get install geoip-bin`\).
  * Produz output na forma de CSV com um header no formato "Count, IP, Location".
* Implementado como:

```bash
#!/bin/bash

declare -r FILE_PATH=$1
declare -r LIMIT=10

log() {
    local ENTRY="${@}"
    echo "$ENTRY" >> ips.out

    return 0
}

if [[ -z "$FILE_PATH" || ! -e "$FILE_PATH" ]]; then
    echo "ERROR: File [$FILE_PATH] does not exist or could not be read!" >&2
    exit 1
fi

rm ips.out
log "Count,IP,Location"

cat "$FILE_PATH" | grep "Failed" | awk -F 'from ' '{ print $2 }' | cut -d ' ' -f 1 | sort | uniq -c | sort -nr | while read COUNT IP 
do
    LOCATION=$(geoiplookup "$IP" | awk -F ', ' '{ print $2 }')

    log "${COUNT},${IP},${LOCATION}"

    if [[ "$COUNT" -gt "$LIMIT" ]]; then 
        echo "WARN: The ip [$IP] from [$LOCATION] tried to login [$COUNT] times unsuccessfully, and it may be a potential attacker!" >&2
    fi 

done

exit 0

```

## Seção 8

### Aula 41

* Esta seção explica como executar comandos em outros computadores e, para isso, precisaremos de um ambiente com mais containers. Recomenda-se 3, onde um é o administrador e os outros dois são servidores. É um cenário interessante para sysadmins. Passo. 
* O comando `tee` lê do standard input e escreve no standard output e em arquivos. O `tee` é usado quando queremos redirecionar algo para um arquivo restrito, por exemplo: `echo '10.9.8.11 server01' | sudo tee -a /etc/hosts`. Se o operador de redirecionamento `>>`  fosse usado nesse caso, um erro ocorreria pois o arquivo `etc/hosts` é restrito e não há como usar o operador com `sudo`.
* Para acesso seguro via ssh, recomenda-se gerar um chave com `ssh-keygen`, e copiar a chave gerada para o servidor destino com `ssh-copy-id <hostname>`.

### Aula 42, 43 e 44

* Um exercício de conexão em sistemas remotos e execução de comandos. Passo.

## Mais

* Mais duas seções com aulas de revisão!

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
16. IBM DeveloperWorks - Com getopts, seus scripts ficam mais profissionais: [https://www.ibm.com/developerworks/community/blogs/752a690f-8e93-4948-b7a3-c060117e8665/entry/getopts\_scripts\_mais\_profissionais?lang=en](https://www.ibm.com/developerworks/community/blogs/752a690f-8e93-4948-b7a3-c060117e8665/entry/getopts_scripts_mais_profissionais?lang=en)

## Relacionados

* [Shell Script](../../programacao/linguagens/shell-script/)

