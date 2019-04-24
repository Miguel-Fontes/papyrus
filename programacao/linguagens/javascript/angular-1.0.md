---
description: >-
  A primeira versão do Framework javascript da Google para desenvolvimento de
  SPAs
---

# Angular 1.0

## Introdução

Angular 1.0 é a primeira versão do Framework desenvolvido na Google para a construção de SPAs.

## Services

### Valores

#### Value

Representa um valor. Geralmente é utilizado para definição de constantes interessantes para diversos pontos da aplicação.

```javascript
angular.module("phoneList").value("config", {
    quotesBaseUrl: "https://talaikis.com/api/quotes/"
})
```

#### Constant

É como um valor, no entanto, inicializado em um momento anterior do ciclo de vida, quando comparado com [value](angular-1.0.md#value). Constants devem ser usadas quando for necessário inserir um valor em um service.

```javascript
angular.module("phoneList").constant("contant", {
    // atributos
});
```

#### Config

São usados para a configuração de [providers](angular-1.0.md#provider), sendo possível injetar apenas providers em seu escopo, qualquer outro tipo de service resultará em erros.

```javascript
angular.module("phoneList").config(function(serialGeneratorProvider) {
    serialGeneratorProvider.setLength(100);
});
```

### Factory

Um factory isola uma função que constrói um valor. A analogia é diretamente com um `factory` dos clássicos design patterns.

```javascript
module.factory( 'serviceName', function );
```

### Service

Conforme citado em _Criando Serviços_ \[[Branas82](https://www.youtube.com/watch?v=Y0dF9juoJb8)\] instanciam a função asscociada utilizando o método `new`, e este valor é injetado em seus clientes.

```javascript
module.service( 'factoryName', function );
```

### Provider

É a abstração pai de `Services`, `Factories`, e `Value`. Quando um provider é injetado:

1.A função associada é instanciada com o `new` 1. O método `$get()`, é chamado no resultado.

Em suma `(new ProviderFunction()).$get()` \[[SOSxPxV](https://stackoverflow.com/questions/15666048/angularjs-service-vs-provider-vs-factory)\]. O provider é mais complexto por ser muito mais flexível. Uma das maiores motivações de utiliza-lo é a possibilidade de configura-lo utilizando-se dos hooks de ciclos de vida do Angular \(ver em [Config](angular-1.0.md#config)\).

```javascript
angular.module("phoneList").provider("serialGenerator", function () {
  var _length = 10;

  this.setLength = length => {
    _length = length;
  }

  this.$get = function () {
    return {
      generate: () => {
        var serial = "";

        while (serial.length < _length)
          serial += String.fromCharCode(Math.floor(Math.random() * 64) + 32);

        return serial;
      }
    };
  };
});
```

## Filters

Filtros são componentes utilizados para tranformar dados exibidos na camada view, chamados utilizando a sintaxe de pipes `{{contact.name | name | ellipsis:10}}`. Filters podem receber parâmetros, como o exemplo do `ellipsis:10`, garantindo dinamismo às suas computações.

```javascript
angular.module("phoneList").filter("name", function() {
  return function(input) {
    return input
      .split(" ")
      .map(name => isNameConnector(name) ? name : capitalize(name))
      .join(" ");
  };

  function isNameConnector(string) {
    return /(da|de)/.test(string)
  }

  function capitalize(string) {
    return string.charAt(0).toUpperCase() + string.substring(1);
  }

});


angular.module("phoneList").filter("ellipsis", function() {
  return function(input, size) {
    return input.length <= size ? input : addEllipsis(size, input);
  };

  function addEllipsis(size, string) {
    return string.substring(0, size || 2) + "...";
  }
});
```

## Diretivas

Diretivas são marcadores em um elemento DOM que adicionam um comportamento especificado, ou mesmo transformar totalmente o elemento DOM. Com este recurso, podemos criar elementos customizados, separando responsabilidades em nossa aplicação, aplicar aplicar máscaras, valores e outros.

Uma diretiva é declarada com `angular.module("phoneList").directive("uiAlert", function() {...}`. A função associada retorna um objeto javascrip chamado _Directive Definition Object_, que contêm os dados que definem o funcionamento de uma javascript. As propriedades são:

* Replace: remove o elemento original que recebeu o tag da diretiva, e inclui o template
* Restrict: indica como uma diretiva pode ser utilizada
  * A: indica que uma diretiva só pode ser usada como um atributo de um elemento HTML \(`<div ui-alert></div>`\)
  * E: só pode ser utilizada como elemento \(`ui-alert`\)
  * C: só pode ser utilizada em uma classe CSS \(EXEMPLO\)
  * M: comentário do elemento \(EXEMPLO\)
* Scope: indica o escopo da diretiva, isolando seu escopo dos demais
  * @: passagem por valor
  * =: two way data binding, indicando que a diretiva será notificada de mudanças, e o 'dono' do valor também, caso ocorram.
* Transclude: encapsula conteúdo passado dentro do alemento da diretiva, ativado com `transclude: true`. No template da diretiva, devemos marcar com `ng-transclude` o local onde os dados devem aparecer.
* Require: indica que a diretiva requer o controlador da diretiva informada como argumento. O formato do preenchimento desta diretiva indica como o item será buscado.
  * nome: preenchendo ocm o nome da diretiva como, por exemplo,  `require: ngModel`, o controlador da diretiva será passado.
  * ^nome: neste formato, o controlador do elemento nomeado 'nome' que é o pai do elemento desta diretiva será buscado. O carctere `^` pode ser repetido várias vezes, indicando subida de um nível na árvore do DOM.
* Controller: define um controlador para a diretiva. Esta função será chamada com `new`, portanto, não use lambdas.
* link: define uma função que será executada assim que a diretiva for construída \(após o template, se houve um\). Permite que seja feita alterações no DOM, e recebe os argumentos `required`, o que permite a comunicação entre diretivas.

```javascript
angular.module("phoneList").directive("uiAlert", function() {
  var template = `
    <div class='ui-alert'>
      <div class='ui-alert-title'>
          {{title}}
      </div>
      <div class='ui-alert-message'>
          {{error}}
      </div>
    </div>`;

  return {
    template: template,
    replace: true,
    restrict: "E",
    scope: {
        title: "@",
        message: "="
    },
    //templateUrl: "view/alert.html"
  };
});

angular.module("phoneList").directive("uiAccordions", () => {
    return {
        controller: function ($scope, $element, $attrs) {
            let accordions = [];

            this.registerAccordion = accordion => {
                accordions.push(accordion);
            }

            this.closeAll = () => {
                accordions.forEach(accordion => accordion.isOpened = false);
            }
        }
    }
})

angular.module("phoneList").directive("uiAccordion", () => {
    const template = `
    <div class="ui-accordion-title" ng-click="open()">
      {{title}}
    </div>
    <div class="ui-accordion-content" ng-transclude ng-show="isOpened"></div>
    `

    return {
        template: template,
        transclude: true,
        scope: {
            title: "@"
        },
        require: "^uiAccordions",
        link: (scope, element, attrs, ctrl) => {
            ctrl.registerAccordion(scope);
            scope.open = () => {
                ctrl.closeAll();
                scope.isOpened=!scope.isOpened;
            }
        }
    }
})
```

## Módulos

Módulos são conjuntos de funcionalidades dentro de um mesmo contexto. Geralmente só criamos um módulo por aplicação, mas o Angular permite muito mais que isso. Para criar um módulo ou localiza-lo, usamos:

```javascript
    // Criando um módulo
    angular.module("nomeDoModulo", []);

    // Buscando um módulo
    angular.module("nomeDoModulo");
```

### Template Cache

Para distribuir um módulo, temos que solucionar o problema de como distribuir um template. Não podemos mais deixar o template em uma pasta com um arquivo html, já que desta forma será impossível usa-los em outras aplicações. A alternativa adotada é incluir o HTML no Javascript, o que pode ser feito na diretiva, ou de forma central usando `$templateCache`, que salva um template em memporia. A recomendação de uso é a seguinte:

```javascript
angular.module("ui").run(() => {
    const accordionTemplate = `
    <div class="ui-accordion-title" ng-click="open()">
      {{title}}
    </div>
    <div class="ui-accordion-content" ng-transclude ng-show="isOpened"></div>
    `;

  $templateCache.put("view/accordion.html", accordionTemplate);
});
```

## ngRoute

## Referências

* [Série Angular JS, com Rodrigo Branas](https://www.youtube.com/watch?v=_y7rKxqPoyg)
* [AngularJS: Service vs provider vs factory](https://stackoverflow.com/questions/15666048/angularjs-service-vs-provider-vs-factory)
* [Angular Style Guide](https://github.com/johnpapa/angular-styleguide/blob/master/a1/README.md#single-responsibility)
* [Angular Docs - Directives](https://docs.angularjs.org/guide/directive)
* [Angular Developer Guide](https://docs.angularjs.org/guide)
* [Angular API Reference](https://code.angularjs.org/1.7.5/docs/api)
* [Angular Error Reference](https://code.angularjs.org/1.7.5/docs/error)

