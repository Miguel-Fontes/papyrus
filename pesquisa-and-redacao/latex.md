---
description: >-
  Sistema de marcação de documentos que permite que o autor não se preocupe com
  a formatação enquanto escreve o documento.
---

# Latex

## Introdução

Latex é um sistema de preparação de documentos, que nos provê uma 'linguagem' de marcação que definir a estrutura de um documento de texto. Através destas marcações, é possível definir em um segundo momento como o documento será formatado, evitando interrupções durante a escrita e até mesmo removendo esta responsabilidade do autor do documento. Isto faz do Latex uma ferramenta muito utilizada para a escrita de artigos e livros. 

## Básicos

### Estruturando Documentos
https://en.wikibooks.org/wiki/LaTeX/Document_Structure 

### Referência Cruzada
https://en.wikibooks.org/wiki/LaTeX/Labels_and_Cross-referencing

https://en.wikibooks.org/wiki/LaTeX/Bibliography_Management

https://pt.sharelatex.com/learn/Bibliography_management_with_biblatex
https://en.wikibooks.org/wiki/LaTeX/Bibliography_Management

| Prefixo | Tipo                 |
| ------- | -------------------- |
| ch      | chapter              |
| sec     | section              |
| subsec  | subsection           |
| fig     | figure               |
| tab     | table                |
| eq      | equation             |
| lst     | code listing         |
| itm     | enumerated list item |
| alg     | algorithm            |
| app     | appendix subsection  |


### BibTex
Utilizando o biblatex, após adicionar um arquivo *.bib no diretório do Projeto, é necessário incluir o pacote `\usepackage[backend=biber]{biblatex}` passando o Backend como biber.

Feito isto, processa-se o arquivo *.tex do projeto e, após, utiliza-se o biber para executar o arquivo *.bcf gerado pelo `latex`. Geralmente a ordem de execução é:

```
latex arquivo.tex
biber arquivo.bcf
```

Podemos imprimir todas as referências com o comando `\printbibliography[title={Referências}]`. Para citar uma das referências, utilizamos o comando `\cite{noauthor_maven_nodate}` onde `noauthor_maven_nodate` é o nome da chave bib do arquivo *.bib.

### Espaçamento entre Parágrafos

https://pt.sharelatex.com/learn/Paragraph_formatting

### Internacionalização

    \usepackage[utf8]{inputenc}
    \usepackage[T1]{fontenc}
    \usepackage[brazilian]{babel}

Wiki: https://en.wikibooks.org/wiki/LaTeX/Internationalization
Documentação do Pacote: https://www.ctan.org/pkg/babel

### Incluindo Arquivos Latex
Podemos incluir arquivos Latex em outros arquivos Latex e esta é uma das principais forças da plataforma. Geralmente, para livros, os capítulos são separados cada um em seu próprio arquivo e o arquivo principal os importa e define a formatação como um todo.

OS principais comandos de inclusão são `\include` e `\input`. Estes dois comandos diferem da seguinte forma:
- O comando `\include` efetua a inclusão do arquivo referenciado e adiciona um page break antes e depois de seu conteúdo, o que o torna perfeito para capítulos de livros. Uma nota importante é que um arquivo incluído via `\include` não pode executar `\include` em outros arquivos (mas pode usar um `\input`).
- O comando `\input` inclui o conteúdo de um arquivo tex referenciado (sem adicionar page breaks), o que o torna uma boa opção para subseções de grandes artigos. Um arquivo incluso via `\input` pode efetuar `\input` em outros arquivos.

### Título do Sumário
Por padrão, o sumário é nomeado em inglês como "Table of Contents". Para alterar o título do sumário, devemos utilizar o comando:

    \renewcommand{\contentsname}{Sumário}

### Estrutura básica de projeto


### Tabelas
https://en.wikibooks.org/wiki/LaTeX/Tables

    \begin{tabular}{|l|l|p{10cm}|}

O Package tabularx ajuda na construção de tabelas, adicionando um parâmetro x que irá extender o tamanho da célula tanto quanto necessário pelo texto contido nela, evitando o uso do parâmetro `p` e sua definição manual de tamanho.

```
\usepackage{tabularx}
\begin{table}[H]
 \centering
 \begin{tabularx}{\textwidth}{|l|l|X|}
 \hline
 \textbf{Operador} & \textbf{Operação} & \textbf{Exemplo}                                                        \\ \hline
 +                 & Soma              & int resultado = 5 + 5 \newline // resultado = 10                        \\ \hline
 -                 & Subtração         & int resultado = 5 - 5 \newline // resultado = 0                         \\ \hline
 *                 & Multiplicação     & int resultado = 5 * 5 \newline // resultado = 25                        \\ \hline
 /                 & Divisão           & int resultado = 5 / 5 \newline // resultado = 1                         \\ \hline
 \textasciicircum  & Exponenciação     & int resultado = 5 \textasciicircum\space 5 \newline // resultado = 3125 \\ \hline
 \%                & Resto (modulus)   & int resultado = 5 + 5 \newline // resultado = 0                         \\ \hline
 \end{tabularx}
\end{table}
```
### Adicionando Imagens
https://en.wikibooks.org/wiki/LaTeX/Importing_Graphics


### Símbolos Matemáticos
http://artofproblemsolving.com/wiki/index.php?title=LaTeX:Symbols

### Floats
Pode acontecer com imagens e tabelas
\usepackage{float}
\begin{table}[H]

## Pacotes
https://en.wikibooks.org/wiki/LaTeX/Package_Reference


### Utilidades

#### Todo

``` latex
\usepackage{todonotes}
\todo{Refazer esta secao completamente\ldots}
\listoftodos
```

### Memoir

https://ctan.org/pkg/memoir

## Latex for Source Code
Para importar Source code para o Latex, usamos listings.

### Incluir o Pacote Listings
Para incluir o pacote, usamos a seguinte instrução:

    \usepackage{listings}

### Incluir código estático

```
\begin{lstlisting}
public class App {
   public static void main(String[] args) {
       System.out.println("Ola Mundo!");

       byte um = 1;
       short dois = 2;
       int tres = 3;
       long quatro = 4;

       System.out.println("-- Integers ---");
       System.out.println("byte  \t|\t" + um);
       System.out.println("short \t|\t" + dois);
       System.out.println("int   \t|\t" + tres);
       System.out.println("long  \t|\t" + quatro);
   }
}
\end{lstlisting}
```

### Importar de Arquivo

Inclui o conteúdo de um arquivo diretamente no documento no momento da compilação, sendo extremamente útil para propagar alterações feitas no código diretamente para a documentação.

    \lstinputlisting[language=Java]{/home/miguel/dev/java-pragmatico/src/main/java/com/miguelmf/pragmatico/capitulo1/primitivos/Primitivos.java}

É possível também especificar um escopo:

    \lstinputlisting[language=Python, firstline=37, lastline=45]{source_filename.py}

Fonte: https://en.wikibooks.org/wiki/LaTeX/Source_Code_Listings


### Aparência do Código
É possível configurar a aparência do código com diversas configurações. Existem diversas [configurações disponíveis](https://pt.sharelatex.com/learn/Code_listing) mas, um exemplo, é conforme abaixo:

```
\usepackage{listings}
\usepackage{color}

\definecolor{mygreen}{rgb}{0,0.6,0}
\definecolor{mygray}{rgb}{0.5,0.5,0.5}
\definecolor{mymauve}{rgb}{0.58,0,0.82}

\lstset{ %
  backgroundcolor=\color{white},   % choose the background color; you must add \usepackage{color} or \usepackage{xcolor}; should come as last argument
  basicstyle=\footnotesize,        % the size of the fonts that are used for the code
  breakatwhitespace=false,         % sets if automatic breaks should only happen at whitespace
  breaklines=true,                 % sets automatic line breaking
  captionpos=b,                    % sets the caption-position to bottom
  commentstyle=\color{mygreen},    % comment style
  deletekeywords={...},            % if you want to delete keywords from the given language
  escapeinside={\%*}{*)},          % if you want to add LaTeX within your code
  extendedchars=true,              % lets you use non-ASCII characters; for 8-bits encodings only, does not work with UTF-8
  frame=single,	                   % adds a frame around the code
  keepspaces=true,                 % keeps spaces in text, useful for keeping indentation of code (possibly needs columns=flexible)
  keywordstyle=\color{blue},       % keyword style
  language=Octave,                 % the language of the code
  morekeywords={*,...},            % if you want to add more keywords to the set
  numbers=left,                    % where to put the line-numbers; possible values are (none, left, right)
  numbersep=5pt,                   % how far the line-numbers are from the code
  numberstyle=\tiny\color{mygray}, % the style that is used for the line-numbers
  rulecolor=\color{black},         % if not set, the frame-color may be changed on line-breaks within not-black text (e.g. comments (green here))
  showspaces=false,                % show spaces everywhere adding particular underscores; it overrides 'showstringspaces'
  showstringspaces=false,          % underline spaces within strings only
  showtabs=false,                  % show tabs within strings adding particular underscores
  stepnumber=2,                    % the step between two line-numbers. If it's 1, each line will be numbered
  stringstyle=\color{mymauve},     % string literal style
  tabsize=2,	                   % sets default tabsize to 2 spaces
  title=\lstname                   % show the filename of files included with \lstinputlisting; also try caption instead of title
}
```

### Pacote de Algoritmos

Existe um pacote chamado [Algorithms](https://en.wikibooks.org/wiki/LaTeX/Algorithms) que contém diversas funções para escrever pseudocódigo.

## Snippets

### Inclusão de Código simplificada

    % MACROS
    % Comando com 3 argumentos: Diretório do Projeto, Pacote e Arquivo
    % \includecode{/home/miguel/dev/java-pragmatico/src/main/java/com/miguelmf/pragmatico/}{capitulo1/primitivos/}{Primitivos.java}
    \newcommand{\includecode}[3]{\lstinputlisting[caption=#3, escapechar=, language=Java]{#1#2#3}}

    % Fixando o diretório do projeto diretamente em um novo macro, para facilitar o uso.
    % \includeexample{capitulo1/primitivos/}{Primitivos.java}
    \newcommand{\includeexample}[2]{\includecode{/home/miguel/dev/java-pragmatico/src/main/java/com/miguelmf/pragmatico/}{#1}{#2}}


### Referência Completa

Precisa do pacote Hyperref.

    % Comando recebe label de seção como argumento e gera uma referência completa
    % Ex: \fullref{sec:configurando_ambiente} => 3.1 Configurando ambiente Java
    \newcommand{\fullref}[1]{\hyperref[#1]{\ref*{#1}\space\nameref*{#1}}}

### Operadores Java

    % Comando facilita a inclusao de um OR em Java
    % boolean teste1 = 2 == 1 \javaor 1 == 1 \newline => boolean teste1 = 2 == 1 || 1 == 1 
    \newcommand{\javaor}{\textbar\textbar\space}

    % Comadnos para less than, less than or equal, greater than, greater than or equal.
    \newcommand{\lt}{\textless\space}
    \newcommand{\lte}{\textless = \space}
    \newcommand{\gt}{\textgreater\space}
    \newcommand{\gte}{\textgreater = \space}
### Configurações Básicas

```
    \documentclass{article}

    \usepackage[utf8]{inputenc}
    \usepackage[T1]{fontenc}
    \usepackage[brazilian]{babel}
    \usepackage{csquotes}
    \usepackage{listings}
    \usepackage{color}
    \usepackage{import} 
    \usepackage{hyperref}
    \usepackage[backend=biber]{biblatex}
    \usepackage{todonotes}
    \usepackage{float}

    % Espaçamento entre parágrafos
    \setlength{\parskip}{0.5em}

    % Adiciona um arquivo bibtex chamado referencias.bib
    \addbibresource{referencias.bib}

    % Configuração de code highlighting Java
    \definecolor{dkgreen}{rgb}{0,0.6,0}
    \definecolor{gray}{rgb}{0.5,0.5,0.5}
    \definecolor{mauve}{rgb}{0.58,0,0.82}

    \lstset{
    frame=tb,
    language=Java,
    aboveskip=3mm,
    belowskip=3mm,
    showstringspaces=false,
    columns=flexible,
    captionpos=t,
    basicstyle={\small\ttfamily},
    numbers=left,
    numberstyle=\tiny\color{gray},
    keywordstyle=\color{blue},
    commentstyle=\color{dkgreen},
    stringstyle=\color{mauve},
    breaklines=true,
    breakatwhitespace=true,
    tabsize=3
    }

    \title{Maven e Reusabilidade de Código para o mundo Open Source}
    \date{2017-07-18}
    \author{Miguel Fontes}

    \renewcommand{\contentsname}{Sumário}

    \begin{document}
    \maketitle
    \pagenumbering{arabic}
    \newpage
    
    \tableofcontents
    \newpage
    
    \include{./tex/introducao}
    \include{./tex/pesquisa}
    \include{./tex/conclusao}
    
    \printbibliography[title={Referências}]
    \listoftodos 
    
    \end{document}
```

## Referências e links

- [Wikipedia - Latex](https://pt.wikipedia.org/wiki/LaTeX)
- [Estrutura de documento](https://en.wikibooks.org/wiki/LaTeX/Document_Structure )/
- [Referência cruzada](https://en.wikibooks.org/wiki/LaTeX/Labels_and_Cross-referencing)
- [Biografias](https://en.wikibooks.org/wiki/LaTeX/Bibliography_Management)
- [Formatação de parágrafos](https://pt.sharelatex.com/learn/Paragraph_formatting)
- [Internacionalização](https://en.wikibooks.org/wiki/LaTeX/Internationalization)