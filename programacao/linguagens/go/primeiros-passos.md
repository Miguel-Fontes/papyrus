---
description: Os primeiros passos na linguagem Go!
---

# Primeiros passos

## Introdução

Tudo que é necessário entender para iniciar na linguagem Go, construindo um ambiente de desenvolvimento e o primeiro projeto!

## Estrutura de Projeto

### Workspaces

Um projeto Go deve ser organizado dentro de um Workspace, um diretório com subdiretórios que segue a seguinte convenção:

* bin: diretório onde serão posicionados os binários de aplicações já construídas.
* pkg: contẽm os pacotes packages compilados
* src: contêm o código fonte, separando-os em em packages

O ambiente provido pelo Workspace é a base para a organização para aplicações Go. Geralmente, um desenvolvedor possui um único workspace em sua estação e o utiliza para todos as suas necessidades. Por padrão, o Go espera que o Workspace esteja posicionado na Home do usuário em um diretório nomeado `go` \(`$HOME/go`, em sistemas UNIX, ou `%USERPROFILE%\go` em sistemas Windows\)\[7\].

### Configurando o Go

Para que o Go funcione adequadamente em seu projeto, é importante que as variáveis de ambiente GO estejam configuradas. No momento da instalação do Go, o diretório raiz onde se encontra o aplicativo deverá ser adicionado ao PATH, para facilitar seu acesso:

```text
export PATH=$PATH:/usr/lib/go-1.9/bin/
```

Como citado na seção mas anterior, o Go espera já configura um Workspace padrão e espera que este seja utilizado, mas é possível trabalhar em um local diferente. Para isto é necessário alterar a variável de ambiente GOPATH e, para facilitar a execução de binários \(os executáveis de aplicações Go\), o PATH:

```text
export GOPATH=/home/miguel/dev/go-learning/
export PATH=$PATH:/home/miguel/dev/go-learning/bin/
```

### Criando um Projeto

No Workspace selecionado, devemos adicionar os projetos Go no diretório `src`. Um projeto Go é separado em pacotes e recomenda-se que estes sejam agrupados em um esquema de nomes que evite conflito com outros nomes já existentes. Uma prática muito comum é usar seu identificador de um repositório para identifica-lo unicamente como, por exemplo, `github.com/miguelmf/greeter`. No entanto, qualquer nome que não entre em conflito com os nomes dos packages padrão pode ser utilizado.

```text
├── bin
│   └── greeter
├── pkg
└── src
    └── com.miguelmf
        └── greeter
            └── greeter.go
```

### Criando um Entry Point

O Entry Point de toda aplicação deve ser uma função chamada `main` que faz parte do um package `main`. Para exemplificar, o conteúdo do arquivo `greeter.go` será:

```go
    package main

    func main() {
        println("Olá Mundo")
    }
```

A existência deste arquivo no pacote main, fará com que o Go crie um exectuável para nossa aplicação quando executarmos o comando `go install`.

### Compilando e Execuando um Projeto

Para compilar e criar um binário de um módulo devemos, no diretório raiz do Workspace \(o que contêm os diretórios bin, pkg e src\), executar o comando `go install <nome-modulo>`.

Isto fará com que o Go compile o módulo com o nome indicado e os módulos necessários para seu funcionamento. Os binários construídos serão posicionados no diretório bin e poderão ser executados diretamente. Utilizando o exemplo anterior, deve-se executar:

```text
go install com.miguelmf/greeter
greeter
```

Ao executar o comando `greeter` será impresso `Olá Mundo` no console, confirmando o funcionamento da aplicação.

Caso ocorram erros na execução do projeto, verifique a configuração de suas variáveis de ambiente.

### Modularização

Para modularizar código Go, separamos o código funcionalmente em diferentes pacotes. Para exemplificar, para criar um biblioteca que reverte Strings e consumi-la em nosso Greeter, temos de criar um novo módulo que será nomeado como `stringutil` dentro do diretório `com.miguelmf`.

```text
├── bin
│   └── greeter
├── pkg
└── src
    └── com.miguelmf
        ├── greeter
        │   └── greeter.go
        └── stringutil
            └── reverse.go
```

O conteúdo do arquivo reverse.go será:

```go
    // Package stringutil contains utility functions for working with strings. Credits: https://golang.org/doc/code.html#Library
    package stringutil

    // Reverse returns its argument string reversed rune-wise left to right.
    func Reverse (s string) string {
        r := []rune(s)

        for i, j := 0, len(r)-1; i<len(r)/2; i, j = i+1, j-1 {
            r[i], r[j] = r[j], r[i]
        }

        return string(r)
    }
```

Para consumir esta função no `main`, precisamos aplicar as seguintes alterações:

```go
    package main

    import (
        "com.miguelmf/stringutil"
    )

    func main() {
        greet := "Olá Mundo"

        println(greet)
        println(stringutil.Reverse(greet))
    }
```

Ao executar os comandos de build agora, o Go se encarregará de efetuar o build de quaisquer dependências, se necessário.

```text
go install com.miguelmf/greeter
greeter
```

### Mais sobre importação de Pacotes

Para importar código no Go, utilizamos o comando `import`. Este comando, recebe uma lista de Strings, representando as dependências à importar, separadas por linhas \(uma em cada linha\). Caso seja necessário adicionar um Alias para o pacote importado, adiciona-se o nome desejado antes do nome do pacote.

```go
    import (
        "fmt"
        u "util/console"
    )
```

o Go é estrito no quesito de importação. Se algo for importado e não for utilizado, haverá um erro de compilação!

### Testes

O Go possui uma biblioteca de testes padrão chamada `testing`. Testes escritos com esta biblioteca podem ser executados facilmente com o comando `go test`. Testes são adicionados no mesmo diretório dos arquivos que contêm as funçôes que testam. Usando nosso exemplo, criamos o arquivo `reverse_test.go` em `com.miguelmf/stringutil`.

```text
├── bin
│   └── greeter
├── pkg
│   └── linux_amd64
│       └── com.miguelmf
│           └── stringutil.a
└── src
    └── com.miguelmf
        ├── greeter
        │   └── greeter.go
        └── stringutil
            ├── reverse.go
            └── reverse_test.go
```

O conteúdo deste arquivo será:

```go
    // Credits to https://golang.org/doc/code.html#Testing
    package stringutil

    import "testing"

    func TestReverse(t *testing.T) {
        cases := []struct {
            in, want string
        }{
            {"Hello, world", "dlrow ,olleH"},
            {"Hello, 世界", "界世 ,olleH"},
            {"", ""},
        }
        for _, c := range cases {
            got := Reverse(c.in)
            if got != c.want {
                t.Errorf("Reverse(%q) == %q, want %q", c.in, got, c.want)
            }
        }
    }
```

Note que a biblioteca de testes não fica em nosso caminho, nos provendo somente os meios necessários para indicar sucesso ou falha do teste e o keywork `case`.

Basta agora executar o comando `go test`:

```text
go test com.miguelmf/stringutil
ok      com.miguelmf/stringutil 0.001s
```

## Principais estruturas

### Tipos

Os tipos primitivos de go são:

* bool
* string
* int 
* int8 
* int16 
* int32 
* int64
* uint // unsigned int \(sem sinal +-\)
* uint8 
* uint16 
* uint32 
* uint64 
* uintptr
* byte // alias para uint8
* rune // alias para int32, representa um código Unicode
* float32
* float64
* complex64
* complex128

Nesta lista, os tipos `int` com limite de tamanho \(int8, uint8,...\) só devem ser usados caso exista uma real necessidade de se ter uma limitação de tamanho.

### Variáveis

Variáveis são declaradas com o keyword `var` ou o operador `:=`.

```text
var nome string = "Nome"
outroNome := "Outro Nome"
```

O operador `:=` possui a capacidade de inferir o tipo da variável diretamente. Outro ponto importante é que é possível declarar múltiplas variáveis em ambos os métodos.

```text
var nome, nomes string = "Miguel Fontes", "nomes"
umNome, outroNome := "umNome", "Outro Nome"
```

### Structs

```go
    type Node struct {
        Value int
        Left  *Node
        Right *Node
    }
```

### Interfaces

### Erros

A caller passing a negative argument to Sqrt receives a non-nil error value \(whose concrete representation is an errors.errorString value\). The caller can access the error string \("math: square root of..."\) by calling the error's Error method, or by just printing it:

```go
    f, err := Sqrt(-1)
    if err != nil {
        fmt.Println(err)
    }
```

* Mensagens de erro Go devem estar todas em letras minúsculas e não devem ser terminadas em pontuação.
* Um pacote de tratamento de erros muito popular é o "github.com/pkg/errors"
* A possibilidade de tratamento de erros de maneira monádica adiciona muita flexibilidade ao gerenciamento de erros do Go

## Documentação de código

[Ver o GoDoc](https://blog.golang.org/godoc-documenting-go-code)\[13\]

## Estilo de Código

### Visibilidade de Funções

Funções públicas deverão ter seus nomes iniciando em letra maiúscula, enquanto as privadas, minúscula.

* Greet: pública
* greet: privada

### Nomes

Tipos e Variáveis terão sempre nomes iniciando em letras minúsculas e recomenda-se que tenham nomes curtos, mas com significado.

### Pacotes e Funções

Recomenda-se que pacotes e funções tenham nomes curtos e compostos de uma única palavra. Caso exista necessidade de mais contexto no nome, utilize-se da junção do nome do pacote com o nome da função. Deve-se sempre lembrar que para referir-se à uma função de um pacote importado, será sempre necessário fazer referência ao pacote, o que garante a presença do nome do pacote nas expressões, adicionando semântica.

Um exemplo muito utilizado é o `bytes.Buffer`. O pacote `bytes` contêm funções relacionadas à `bytes` e sua função `Buffer` é um buffer de `bytes`, eliminando a necessidade de nomear a função como ByteBuffer ou algo similar.

### Outros

Além disto, o Go é formatado automaticamente pelo comando `go fmt`, o que garante que o código de todos os programadores siga as mesmas regras.

### Getter e Setters

Quando houver necessidade de criar getters e/ou setters, a convenção de nomes destas funções em Go é:

* Getter: deve ter o nome do campo, sem `get` \(Ex: um campo nome, terá um getter `Nome`\). 
* Setter: deve ser a função de `Set` com o nome do campo \(Ex: um campo nome, terá um setter `SetNome`\).

## Editores e IDEs

### Visual Studio Code

O Visual Studio Code possui suporte à linguagem GO, sendo necessário instalar alguns plugins, sugeridos pela própria aplicação ao abrir um código fonte Go. Adicionalmente, possível configurar o GO Path da aplicação e um Root para pesquisar pro Go Tools no Workspace Settings\[12\], através das chaves `go.gopath` e `go.toolsGopath`.

```text
"go.gopath": "/home/miguel/dev/go-learning/"
```

## Referências

\[1\] Learn X in Y Minutes Where X = Go [https://learnxinyminutes.com/docs/go/](https://learnxinyminutes.com/docs/go/)

\[2\] An Introduction to Programming in Go [http://www.golang-book.com/books/intro](http://www.golang-book.com/books/intro)

\[3\] Go Standard Library Docs [https://golang.org/pkg/fmt/](https://golang.org/pkg/fmt/)

\[4\] How to Write Go Code [https://golang.org/doc/code.html](https://golang.org/doc/code.html)

\[5\] Writing Modular Go Programs with Plugins [https://medium.com/learning-the-go-programming-language/writing-modular-go-programs-with-plugins-ec46381ee1a9](https://medium.com/learning-the-go-programming-language/writing-modular-go-programs-with-plugins-ec46381ee1a9)

\[6\] Go Plugins [https://golang.org/pkg/plugin/](https://golang.org/pkg/plugin/)

\[7\] The GOPATH environment variable [https://golang.org/doc/code.html\#GOPATH](https://golang.org/doc/code.html#GOPATH)

\[8\] Go Lang Spec [https://golang.org/ref/spec](https://golang.org/ref/spec)

\[9\] Effective GO [https://golang.org/doc/effective\_go.html](https://golang.org/doc/effective_go.html)

\[10\] String Formatting in Go [https://gobyexample.com/string-formatting](https://gobyexample.com/string-formatting)

\[11\] Error Handling in Go [https://blog.golang.org/error-handling-and-go](https://blog.golang.org/error-handling-and-go)

\[12\] VSCode and GO: Setting the Go Path [https://github.com/Microsoft/vscode-go/wiki/GOPATH-in-the-VS-Code-Go-extension\#gopath-from-goinfergopath-setting](https://github.com/Microsoft/vscode-go/wiki/GOPATH-in-the-VS-Code-Go-extension#gopath-from-goinfergopath-setting)

\[13\] GoDoc - Documenting Go Code [https://blog.golang.org/godoc-documenting-go-code](https://blog.golang.org/godoc-documenting-go-code)

\[14\] HTTP Mock - Mocking HTTP Requests in Go Tests [https://godoc.org/github.com/jarcoal/httpmock](https://godoc.org/github.com/jarcoal/httpmock)

\[15\] Godoc Tricks [https://godoc.org/github.com/fluhus/godoc-tricks](https://godoc.org/github.com/fluhus/godoc-tricks)

