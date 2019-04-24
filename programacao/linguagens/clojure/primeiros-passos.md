---
description: Primeiros passos na linguagem Clojure!
---

# Primeiros passos

## Introdução

Notas sobre como iniciar a desenvolver aplicações na linguagem clojure, cobrindo a instação e conceitos básicos. Os básicos da linguagem são do do livro [Clojure for the Brave and True](https://www.braveclojure.com/clojure-for-the-brave-and-true/), que é inteiramente gratuito para leitura online!

## Instalação

Para executar aplicações Clojure, é necessário executar os comandos descritos na seção Getting Started do site oficial\[1\], que é basicamente:

```text
curl -O https://download.clojure.org/install/linux-install-1.9.0.297.sh
chmod +x linux-install-1.9.0.297.sh
sudo ./linux-install-1.9.0.297.sh
```

Lembre-se de verificar o site oficial para garantir de que esteja instalando a versão mais recente. Adicionalmente, utiliza-se uma ferramenta chamada Leiningen para facilitar a construção e gerenciamento de projetos. As instruções de instalação estão descritas no site oficial da ferramenta\[2\], e são similares ao do Clojure:

```text
mkdir /opt/leiningen/ && /opt/leiningen/
wget https://raw.githubusercontent.com/technomancy/leiningen/stable/bin/lein
sudo chmod a+x ./lein
sudo ./lein
sudo ln -s /opt/leiningen/lein /usr/bin/lein
```

## Criando um Projeto

No diretório de sua escolhas

```text
lein new app clojure-first-steps
```

Será construída uma estrutura de diretório similar à:

```text
├── CHANGELOG.md
├── doc
│   └── intro.md
├── LICENSE
├── project.clj
├── README.md
├── resources
├── src
│   └── clojure_first_steps
│       └── core.clj
├── target
│   └── default
│       ├── classes
│       │   └── META-INF
│       │       └── maven
│       │           └── clojure-first-steps
│       │               └── clojure-first-steps
│       │                   └── pom.properties
│       ├── repl-port
│       └── stale
│           └── leiningen.core.classpath.extract-native-dependencies
└── test
    └── clojure_first_steps
```

Esta estrutura é uma convenção da aplicação Leiningen para projetos Clojure. O arquivo `project.clj` presente na raiz do projeto faz o papel de um `pom.xml` em projetos Maven, descrevendo o projeto e definindo suas dependências e outras informações.

O arquivo core.clj é definido como o Entry Point da aplicação no arquivo `project.clj` e é o arquivo onde será buscada a função Main da aplicação. Para executar o projeto da maneira em que está, navegue até a raiz do projeto via terminal e execute:

```text
lein run
```

O Output será "Hello World" conforme definido na função main padrão da aplicação. Ao executar um projeto utilizando o Leinigen, temos a saída desejada no terminal mas essa opção é inviável para executar a aplicação em ambientes em que o leinigen não esteja instalado. Para distribuir a aplicação, o Leinigen possui o comando `uberjar`:

```text
lein uberjar
```

Este comando irá criar um Jar standaone que pode ser executado normalmente em qualquer estação que possua o Java instalado.

## Código Clojure

```text
(defn -main
  "I don't do a whole lot ... yet."
  [& args]
  (println "I'm a little teapot!"))

(if true
  "By Zeus's hammer!"
  "By Aquaman's trident!")

(if false
  "By Zeus's hammer!"
  "By Aquaman's trident!")

(if true
  (do (println "Success!")
      "By Zeus's hammer!")
  (do (println "Failure!")
      "By Aquaman's trident!"))

(when true
  (println "Success!")
  "abra cadabra")
```

## REPL

O clojure é lento para executar uma aplicação, mas a existência do REPL [4](https://clojure.org/guides/repl/launching_a_basic_repl) é o que garante produtividade no desenvolvimento com a linguagem, por isso, o entendimento de seu funcionamento é essencial.

Utilizando o Leinigen, basta executar `lein repl` para acessá-lo.

#### Buscar documentação no REPL

É possível utilizar o REPL para buscar a documentação de funções. Para isso, precisamos importar a library `clojure.repl`. Execute:

```text
(require '[clojure.repl :refer :all])
```

Feito isso, é possível buscar pela documentação de uma função diretamente com `(doc nil?)`, onde `nil?` é uma função. Outra opção é buscar globalmente com `(find-doc "indexed")`, onde novamente `indexed` pode ser alterado por qualquer função.

#### Pretty Printing

Para formatar estruturas de dados no REPL, utiliza-se a library `clojure.pprint`, que pode ser importada e usada da seguinte forma::

```text
user=> (require '[clojure.pprint :as pp])
nil
user=> (pp/pprint (mapv number-summary [5 6 7 12 28 42]
```

Para facilitar, há também uma função chamada `pp` que age como um _shorthand_ para `pprint`.

```text
(pp/pp)
```

#### Analisando exceções

Quando um exception é lançado, uma linha com um resumo do problema será impressa no REPL. Para obter mais informações, deve-se usar a função `clojure.repl/pst`, invocada como `(pst *e)`. Caso necessário, avaliar a excepção também poderá retornar mais informações, o que pode ser feito com `*e`.

## Conceitos Básicos

Todos código Clojure é escrito em dois tipos de estruturas:

* Representações literais de estruturas de dados
* Operações

### Def

É possível usar def para fazer o bind de um nome com um valor:

```text
(def failed-protagonist-names ["Larry Potter" "Doreen the Explorer"])
```

### Maps

Os maps são equivalentes aos hashmaps e dicionários em outras linguagens. Podemos definir um map em sua forma literal da seguinte forma:

```text
{:first-name "First Name"
 :last-name "Last name"}
```

Neste exemplo `:first-name` e `last-name` são keys, valores especiais do clojure que possuem a propriedade de poderem ser utilizados como funções para buscar um valor em um map.

```text
(:first-name {:first-name "Name"})
```

Note que se um valor não for encontrado, será retornado `nil`. Também é possível usar a função `hash-map` para criar mapas, `(hash-map :a 1 :b 2)`. Há também uma função chamada `get-in` para buscar valores em mapas aninhados `(get-in {:a {:b "nope"}} [:a : b]`.

### Vetores e listas

Um vetor usa a clássica bracket notation no CLojure `[3 2 1]`, já listas devem ser definidas com parênteses e um prefixo '"' `'(1 2 3 4)`. Existem algumas diferenças entre listas e arrays, principalmente no quesito de retrieve de seus valores. Com arrays, é possível usar a função `get` passando um índice para obter o valor da posição indicada \(`(get arr 0)`\), já em listas não. Em listas, devemos usar a função `nth`. Vale citar que a função `nth` é mais lenta que a `get`, pois tem de avaliar cada item de uma lista até encontrar o desejado.

Para adicionar valores em arrays ou listas, usamos a função `conj`. Em arrays, esta função fará o append ao fim e, em lista, no início. É possível construir vetores com a função `vector` e listas com a `list`.

### Sets

São listas com valores únicos. A notação literal de hash sets é `#{"kurt 20 :icicle}`, mudando da sintaxe de maps apenas pelo '\#' prefixando o valor. É possível construir hash sets utilizando a função `hash-set`, no formato `(hash-set 1 1 2 2)`, o que resultaria em `#{1 2}`. Há também a função `set` para transformar arrays em sets.

Para verificar se há um valor em um set, podemos usar a função `contains?`.

### Funções

A estrutura de definição de uma função em Clojure é a seguinte:

* defn
* Nome da função
* Um docstring \(optional\)
* Parâmetros listados em colchetes
* Corpo da função

Podemos exemplificar da seguinte forma:

```text
(defn sum
  "Sum two numbers"
  [x y]
  (+ x y))
```

Um recurso importante, é o suporte à multi arity, que pode ser feito da seguinte forma:

```text
(defn multi-arity
  ;; 3-arity arguments and body
  ([first-arg second-arg third-arg]
     (do-things first-arg second-arg third-arg))
  ;; 2-arity arguments and body
  ([first-arg second-arg]
     (do-things first-arg second-arg))
  ;; 1-arity arguments and body
  ([first-arg]
     (do-things first-arg)))
```

Além disso, também é possível indicar que a função recebe uma quantidade variável de argumentos e que eles devem ser recebidos como uma lista, através do uso do operador `&`, que geralmente é compreendido como o 'resto' dos argumentos.

```text
(defn favorite-things
  [name & things]
  (str "Hi, " name ", here are my favorite things: "
       (clojure.string/join ", " things)))
```

É possível também fazer a descontrução de parâmetros, utilizando sintaxe similar à do Haskell. Note no exemplo abaixo, como as variáveis `first-choice` e `second-choice` representam o primeiro e segundo valor de um vetor e, o restantes, ficam em `unimportant-choices`.

```text
(defn chooser
  [[first-choice second-choice & unimportant-choices]]
  (println (str "Your first choice is: " first-choice))
  (println (str "Your second choice is: " second-choice))
  (println (str "We're ignoring the rest of your choices. "
                "Here they are in case you need to cry over them: "
                (clojure.string/join ", " unimportant-choices))))
```

O mesmo é possível para mapas, sendo o segundo exemplo, uma exibição do shorthand `keys`, para trabalhar com mapas. É possível também manter acesso ao mapa completo usando o keyword `:as`.

```text
(defn announce-treasure-location
  [{lat :lat lng :lng}]
  (println (str "Treasure lat: " lat))
  (println (str "Treasure lng: " lng)))


(defn receive-treasure-location
  [{:keys [lat lng] :as treasure-location}]
  (println (str "Treasure lat: " lat))
  (println (str "Treasure lng: " lng))
```

Já no corpo da função, podemos incluir qualquer quantidade de formas, que serão avaliadas. Somente a última é considerada o output da função.

```text
(defn illustrative-function
  []
  (+ 1 304)
  30
  "joe")

(illustrative-function)
; => "joe"
```

#### Funções anônimas

Existem duas formas de criar funções anônimas:

```text
((fn [x] (* x 3)) 8)
; => 24

#(* % 3)
; => 24

(map #(str "Hi, " %)
     ["Darth Vader" "Mr. Magoo"])
; => ("Hi, Darth Vader" "Hi, Mr. Magoo")
```

Na segunda forma, o caracter `%` indica um argumento. No caso da função receber mais de um argumento, é possível referencia-los como `%1`, `%2`, `%3` e assim por diante. O caractere `%` significa `%1`. É possível também usa o operador `&`, como em `%&`.

## Abstrações

### Sequences

Funções que operam sobre listas em clojure utilizam uma abstração chamada sequence, e por vezes isto dificulta o entendimento de quem está iniciando na linguagem, principalmente quando nota-se que a função `map` retorna um valor que não é um vetor, por exemplo. Uma lista é exibida com a notação `(1 2 3 4 5)`, e seus valores são avaliados de forma `lazy`, possibilitando a construção de listas infinitas.

```text
(defn even-numbers
    ([] (even-numbers 0))
    ([n] (cons n (lazy-seq (even-numbers (+ n 2))))))

(take 10 (even-numbers))
; => (0 2 4 6 8 10 12 14 16 18)
```

Note como o retorno não é uma lista, e sim uma sequência. Podemos notar isto com clareza também no seguinte exemplo:

```text
(map #(+ % 1) [1, 2, 3])

; => (2 3 4)
```

## Coleções

A abstração de coleção inclui todas as representações de conjuntos de dados que contenham uma lista de valores. As funções que operam sobre esta abstração, inferem valores com relação à todos os elementos da coleção, como um todo. Exemplos são `count`, `empty?`, e `every?`.

As funções mais interessantes a conhecer de coleções são `into`,

### **Into**

Insere valores em uma estrutura de coleção, ajudando na conversão de volta para a estrutura original.

```text
(into [] (map #(+ % 1) [1, 2, 3]))

; => [2 3 4]
```

### **Conj**

A função `conj` insere elementos em uma lista recebida em seu primeiro argumento, no entatanto, diferente do `into`, os elementos recebidos para insersão devem ser scalars, ou seja, elementos singulares, fora de uma coleção.

```text
(conj [0] 1 2 3 4)

; => [0 1 2 3 4]
```

## Funções de Funções

O clojure possui algumas funções que operam sobre funções, facilitando algumas operações.

### **Apply**

O apply é uma função que deconstrói uma lista em argumentos, fazendo com que seja possível passar os items de uma lista para uma função que aguardaria um rest parameter \(&\).

```text
; max trabalha sobre uma lista de argumentos
(max 1 2 3 4 5 6 7 8 9)
; => 9

; usando o apply, podemos passar um vetor no lugar dos diversos argumentos
(apply max [0 1 2 3 4 5 6 7 8 9])
; => 9
```

### **Partial**

É a função de aplicação parcial do Clojure.

```text
(def add20 (partial + 20))
(add20 2)
; => 22
```

### **Compensate**

É uma função que retorna a composição de `not` com uma outra qualquer.

```text
(def not-done? (compensate done?))
```

## Ferramentas para Functional Programming

### Composição

É feita através da função `comp`, que pode ser usada como `(comp fn fn fn fn)`.

### Memoize

Registra um retorno específico de uma função para que este não seja calculado novamente.

```text
(memoize fn)
```

## Organização do código

A organização de código do Clojure é feita utilizando namespaces, que armazenam os componentes construídos. Internamente, um namespace é um mapa, cujos valores são associados através da palavra chave `def`. Executar o comando `(ns-map *ns*)` imprime todos os itens associados no mapa do Namespace, dando uma excelente ideia de seu funcionamento.

Este aspecto explica porque precisamos prefixar determinadas funções com o nome do 'pacote': para acessar um outro namespace. O Clojure não limita a quantiadade de namespaces possíveis de serem criados, portanto, não há porque limitar-se.

### Require

Para importarmos um outro namespace, devemos utilizar o comando require. Este comando, irá importar o namespace indicado, deixando-o disponível para uso, através de referência com nome totalmente qualificado.

```text
(require [clj-project.something])

(defn my-fn
  []
  (clj-project.something 10))
```

### Refer

Para adicionar funções de outros namespaces, podemos usar `:refer`. Refer traz os objetos de outro namespace para o atual, eliminando a necessidade de fazer referência totalmente qualificada - fazendo um merge dos mapas.

Esta opção pode receber como argumento uma lista de funções à adicionar no namespace atual, ou `:all`, indicando que deseja-se adicionar todas as funções.

```text
(require [clj-project.something :refer [something]])

(defn my-fn
  []
  (something 10))
```

Outra opção disponível é a chave `:as`, possibilitando o o rename de uma namespace ou função.

```text
(require [clj-project.something :as project :refer  [something] :rename {something s}])

(defn my-fn
  []
  (project/s
  (s 10))
```

Note que a sintaxe para usar `require` na definição de um namespace é `:require`.

```text
(ns myproject.core
  (:use [clojure.core] :reload)
  (:require [clojure.string :as str :refer [replace]] :reload-all))
```

Mais sobre isto na \[documentação\)\[[https://clojuredocs.org/clojure.core/require](https://clojuredocs.org/clojure.core/require)\].

## Quotes

Um dos recursos mais interessantes do Clojure, é a possibilidade de passar código não avaliado como argumento para funções. Quando incluímos um `'` antes de qualquer valor, ou chamamos a função `quote`, o código que estamos enviando é puramente um bloco de texto não avaliado.

Iste é um feature interessante para programação em altos níveis de abstração e utilizado largamente pelo próprio clojure.

```text
The intent is to quote the symbols. This way they'll be treated as symbols, and use can take those symbols as naming a namespace to load and pull into the current. You want to avoid the default treatment of a symbol, which is resolving it as the name of a Var and using the value of that Var. You can also do this as

(use ['clojure.string :as 'str])
but that involves some unnecessary typing; quoting the whole vector makes you less likely to forget anything. Particularly if you're doing anything with :only, :refer or similar keyword arguments.

Aside: ns doesn't need this because as a macro it can control evaluation of its arguments - functions like require and use have all their arguments read and evaluated before they themselves run. This is part of the reason why ns is normally preferred over those functions.

Fonte: https://stackoverflow.com/questions/34118554/why-use-single-quote-around-vector-in-use-in-clojure 
```

Em alguns momentos, é possível abusar deste feature para escrever código mais sucinto:

```text
    (is (= '((a a a a) (b) (c c) (a a) (d) (e e e e)) (pack-consec-dups '(a a a a b c c a a d e e e e))))
```

## Leiningen Profiles

[https://github.com/technomancy/leiningen/blob/master/doc/PROFILES.md](https://github.com/technomancy/leiningen/blob/master/doc/PROFILES.md)

## Clojure Script

Existe uma vertente do Clojure chamada [Clojurescript](https://clojurescript.org), que é simplesmente uma forma de compilar clojure diretamente para Javascript. Desta forma, é possível desenvolver aplicações completas usando basicamente apenas uma linguagem de programação.

## Referências e links

* \[1\] Clojure Getting Started [https://clojure.org/guides/getting\_started](https://clojure.org/guides/getting_started)
* \[2\] Leiningen Installation [https://leiningen.org/\#install](https://leiningen.org/#install)
* \[3\] Brave Clojure [https://www.braveclojure.com/](https://www.braveclojure.com/)



