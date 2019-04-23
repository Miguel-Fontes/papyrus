---
description: A principal ferramenta de documentação do universo Java.
---

# Javadoc

## Introdução

Javadoc é a principal ferramenta disponível para os desenvolvedores Java construirem documentações detalhadas dos componentes de aplicações. O Javadoc consistente em comentários adicionados na declaração de classes e métodos utilizando sintaxe estruturada. Diversas IDEs conseguem capturar estes comentários e exibir em tempo de desenvolvimento a documentação e, além disto, a ferramenta consegue gerar o clássico site javadoc com todas as informação do código fonte, gerando uma fonte inestimável de informações para os desenvolvedores.

Este documento é baseado no guia da Oracle entitulado [How to Write Doc Comments for the Javadoc Tool](http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html)

## Formato de um comentário Doc

Um comentário doc é escrito em HTML e deve preceder uma classe, campo, construtor ou declaração de método. Ele é composto de duas partes: uma descrição segurda de tags de bloco como, por exemplo, as seguintes tags:

* @param
* @return
* @see

Um exemplo de doc comment:

```java
/**
* Returns an Image object that can then be painted on the screen. 
* The url argument must specify an absolute {@link URL}. The name
* argument is a specifier that is relative to the url argument. 
* <p>
* This method always returns immediately, whether or not the 
* image exists. When this applet attempts to draw the image on
* the screen, the data will be loaded. The graphics primitives 
* that draw the image will incrementally paint on the screen. 
*
* @param url an absolute URL giving the base location of the image
* @param name the location of the image, relative to the url argument
* @return the image at the specified URL
* @see Image
*/
public Image getImage(URL url, String name) {
    try {
        return getImage(new URL(url, name));
    } catch (MalformedURLException e) {
        return null;
    }
}
```

Para este comentário, será gerado o seguinte HTML:

```text
getImage

public Image getImage(URL url, String name)

Returns an Image object that can then be painted on the screen. The url argument must specify an absolute URL. The name argument is a specifier that is relative to the url argument.

This method always returns immediately, whether or not the image exists. When this applet attempts to draw the image on the screen, the data will be loaded. The graphics primitives that draw the image will incrementally paint on the screen.

Parameters:
    url - an absolute URL giving the base location of the image.
    name - the location of the image, relative to the url argument.
Returns:
    the image at the specified URL.
See Also:
    Image
```

## Doccheck

Para garantir que estes sejam escritos e com qualidade, a oracle disponibiliza uma ferramenta chamada Oracle Doc Check Doclet ou DocCheck, que analisa o código e gera um relatório com erros de tag, estilo e recomenrações de mudança.

## Styleguide

* Use o tag `<code>` para palavras chave e nomes.
  * Palavras chave Java
  * Nomes de packages
  * Nomes de classes
  * Nomes de métodos
  * Nomes de interfaces
  * Exemplos de código
* Utilize links economicamente
  * É possível adicionar links para nomes de api com o tag `{@link}`, mas seja econômico. Não faz sentido adicionar links para todas as referências. 
* Omita parenteses para fazer referência à uma forma geral de métodos
  * Quando um método tem mais de uma forma com diferentes números de argumentos, omita os parênteses completamente ao fazer referências.
  * Isto é importante para que o leitor não confunda com uma forma específica do método.
  * Exemplo: `add(int, object)` e `add(object)`. Ao referenciar o método add genericamente, recomenda-se o uso de apenas `add`
* Utilize sempre a terceira pessoa.
  * "Gets the label" ao invés de "Get the label"
* Descrições de método iniciam com um verbo.
* Descrições de classes interfaces e campos podem omitir sujeitos e simplesmente descrever o objeto.
  * Um botão \(recomendado\)
  * Este campo é um botão \(evitar\)
* Use this para objetos criados na classe atual
* Adicione descrição além do nome da API.
  * Os melhores nomes para uma API descrevem completamente o que a API faz, ou seja, uma descrição nem sempre é necessária. 
  * Em alguns casos, desenvolvedores simplesmente repetem o que o nome da API já diz em um comentário Javadoc e isto deve ser evitado.
  * Questione-se o que o método faz, o contexto geral e, então, escreva uma descrição mais abrangente que efetivamente adicione informação. 

```java
/**
* Registers the text to display in a tool tip. The text 
* displays when the cursor lingers over the component.
*
* @param text the string to display. If the text is null, 
* the tool tip is turned off for this component.
*/
public void setToolTipText(String text) {
```

## Convenções de Tag

Utilize a seguinte ordem para adicionar tags.

1. @author \(apenas classes e interfaces, obrigatório\)
2. @version \(apenas classes e interfaces, obrigatório\)
3. @param \(apenas métodos e construtores\)
4. @return \(apenas methods\)
5. @exception \(@throws é um sinônimo adicionado no Javadoc 1.2\)
6. @see
7. @since
8. @serial \(ou @serialField ou @serialData\)
9. @deprecated \(see [Como e quando marcar APIs como Deprecate](http://docs.oracle.com/javase/7/docs/technotes/guides/javadoc/deprecation/deprecation.html)\)

### @author

Pode ser omitido, uma vez que não é critico.

### @param

É seguida do nome do parâmetro, não do tipo. Por convenção, o primeiro substantivo na descrição é o tipo do parâmetro.

* @param ch o caractere à ser testado
* @param observer o image observer a ser notificado

### @return

Omita o tag `@return` para métodos void e construtores. Inclua-o para todos os outros métodos.

### @deprecated

O padrão a ser utilizado é:

```java
/**
* @deprecated As of JDK 1.1, replaced by
* {@link #setBounds(int,int,int,int)}
*/
```

### @throws

Insira um tag throw descrevendo cada unchecked e checked exception retornada pelo método e que cliente deve querer capturar.

## Referências e links

1. [Javadoc Reference Guide](http://docs.oracle.com/javase/7/docs/technotes/tools/windows/javadoc.html)
2. [How to Write Doc Comments for the Javadoc Tool](http://www.oracle.com/technetwork/java/javase/documentation/index-137868.html)

