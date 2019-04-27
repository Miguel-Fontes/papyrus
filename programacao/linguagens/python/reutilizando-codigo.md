---
description: Crie funções e classes reutilizáveis em Python.
---

# Reutilizando código

## Introdução

Como construir componentes reusáveis em Python, e importá-los em outras pontos de seu projeto e até mesmo outros projetos.

## Código exemplo

Considere que nós tenhamos um belo módulo python que saiba como executar cálculos artiméticos básicos.

{% code-tabs %}
{% code-tabs-item title="operation.py" %}
```python
import arithmetic.basic as ops


class Operation:

    def __init__(self, operation, x, y):
        self.setOperation(operation)
        self.setX(x)
        self.setY(y)

    def setOperation(self, operation):
        if (operation != "+" and operation != "-" and operation != "*" and operation != "/"):
            raise Exception(
                "The operation [{}] is invalid! Please, select one of the supported operations [+, -, *, /]".format(operation))

        self.operation = operation

    def setX(self, x):
        self.guardIsValidInteger(x)
        self.x = int(x)

    def setY(self, y):
        self.guardIsValidInteger(y)
        self.y = int(y)

    def guardIsValidInteger(self, value):
        try:
            int(value)
        except ValueError:
            raise Exception(
                "Given operators [{}] and is not a integer! Please, provide a valid integer input for both operators!".format(value))

    def asString(self):
        return ("({} {} {})".format(self.operation, self.x, self.y))

    def calculate(self):
        result = 0

        if self.operation == "+":
            result = ops.sum(self.x, self.y)
        elif self.operation == "-":
            result = ops.sub(self.x, self.y)
        elif self.operation == "*":
            result = ops.prod(self.x, self.y)
        elif self.operation == "/":
            result = ops.div(self.x, self.y)
        else:
            raise Exception(
                "Unsupported operation [{}]. There may be a application logic error!".format(self.operation))

        return result
```
{% endcode-tabs-item %}

{% code-tabs-item title="basic.py" %}
```python
def sum(x, y):
    return x+y


def sub(x, y):
    return x-y


def prod(x, y):
    return x*y


def div(x, y):
    return x/y
```
{% endcode-tabs-item %}
{% endcode-tabs %}

A classe `Operation`, serve como Dispatcher e é o ponto de entrada de nosso módulo. Um cliente deste módulo, poderá então importar a classe `Operation` e utilizar-se das operações artiméticas básicas. Um cliente exemplo poderia ser implementado da seguinte forma:

{% code-tabs %}
{% code-tabs-item title="main.py" %}
```python
from arithmetic.operation import Operation
import sys


def main():
    guardIsValidInput(sys.argv)
    operation = buildOperation(sys.argv)
    print("{} = {}".format(operation.asString(), operation.calculate()))


def guardIsValidInput(args):
    if len(args) < 4:
        raise Exception(
            "Mandatory arguments not given! Please, inform a arithmetic operation and two operands!")


def buildOperation(args):
    return Operation(args[1], args[2], args[3])


if __name__ == "__main__":
    main()


```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Importação

O Python provê o mecanismo de importação através da palavra chave `import`, possuindo também um formato alternativo `from x import y`, para uso quando deseja-se importar um item específico de um módulo.

### Qualificando um módulo

Para evitar conflitos de nome, ou até mesmo para melhorar a semântica, é possível importar um módulo fazendo um bind à um nome específico. Para tal, deve-se utilizar a palavra chave `as`

```python
import arithmetic.basic as ops 
```

### Importando módulos locais

Para construir o código listado, o projeto foi estruturado da seguinte forma:

```text
py-misc-examples
└── importing
    ├── arithmetic
    │   ├── basic.py
    │   ├── __init__.py
    │   └── operation.py
    └── main.py
```

Neste estrutura de diretório, o grande detalhe é a existência do arquivo `__init__.py` em _arithmetic_. Este arquivo é necessário para que o Python trate este diretório como um pacote. Sem ele, a importação utilizando `from arithmetic.operation import Operation` não seria possível. Este formato, é chamado de _pacote regular_\[1\].

Construída esta estrutura, a importação pode ser feita exatamente como é feito com as bibliotecas padrão do python.

### Importanto em outros projetos

Se fosse necessário que outro projeto tivesse acesso à estas mesmas funcionalidades, o módulo _arithmetic_ deveria ser extraído deste projeto, tornando-se um projeto isolado. 

```text
 arithmetic
   ├── basic.py
   ├── __init__.py
   └── operation.py
```

Feito isto, devemos trabalhar com uma configuração do Python: o `PYTHONPATH`\[2\].

O `PYTHONPATH` é uma variável de ambiente similar à `PATH`, seu valor consiste de uma lista de diretórios utilizada na busca de módulos python quando uma instrução `import` é avaliada. Neste caso, deveríamos incluir nesta variável o local do módulo _arithmetic_ e, com isso, poderíamos importá-lo em qualquer projeto.

### Distribuindo via package manager

TBD

## Referências e Links

* \[1\] [Python Docs - Packages](https://docs.python.org/3/reference/import.html#packages)
* \[2\] [Python Docs - PYTHONPATH](https://docs.python.org/3/using/cmdline.html#envvar-PYTHONPATH)
* \[3\] [Código exemplo em repositório py-playground](https://github.com/Miguel-Fontes/py-playground/tree/master/importing)

