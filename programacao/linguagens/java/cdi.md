---
description: >-
  CDI (Context and Dependency Injection) é a implementação de "Injeção de
  Dependências" do Java.
---

# CDI

## Introdução

CDI \(Context and Dependency Injection\) é a implementação de "Injeção de Dependências" do Java. Utilizando injeção de dependências podemos atingir a Inversão de Controle \(IoC\) tão necessária para aplicações modulares. A especificação do CDI pode ser encontrada no [site oficial](http://cdi-spec.org/). A implementação referência é o [Weld](http://weld.cdi-spec.org/).

## Habilitando o CDI

O CDI pode ser utilizado de duas formas: via Servidor de Aplicação JEE \(fazendo com que a configuração possa alterar-se\) ou através da configuração manual do Container Weld \(inclusive em aplicações Java SE\).

### Servidores de Aplicação Java EE

A mais simples é com o `Wildfly` onde o Weld já vem integrado, tornando-se necessário apenas adicionar um arquivo `beans.xml` vazio ao diretório `META-INF` \(JAR\) ou `WEB-INF`\(WAR ou EAR\). O arquivo `beans.xml` pode permanecer sem conteúdo, mas é boa prática adicionar a seguinte estrutura:

```xml
    <beans xmlns="http://java.sun.com/xml/ns/javaee"    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
           xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
           http://java.sun.com/xml/ns/javaee/beans_1_0.xsd">
    </beans>
```

Feito isto, o `Wildfly` irá localizar todos os Beans \(segundo a especificação `JavaBeans`\) e disponibiliza-los para injeção. Um bean disponível para injeção é chamado `Managed Bean`.

Para utilizar as anotações do CDI, deve-se adicionar a dependência da API CDI do Java EE ao projeto.

```xml
    <dependency>
       <groupId>javax.enterprise</groupId>
       <artifactId>cdi-api</artifactId>
       <!-- Use version 2.0 for Weld 3 -->
       <version>1.2</version>
       <scope>provided</scope>
    </dependency>
```

Para configuração de outros Servidores de Aplicação, veja a [documentação](http://docs.jboss.org/weld/reference/latest-master/en-US/html/environments.html#weld-servlet) do `Weld`.

### Java SE

Precisamos adicionar o Weld como dependência e requisitar o nosso objeto de inicialização da aplicação ao `Container` do CDI, que irá se encarregar do restante do trabalho. O `beans.xml` no diretório `META-INF` também é necessário e pode estar vazio.

```xml
    <dependency>
        <groupId>org.jboss.weld.se</groupId>
        <artifactId>weld-se-core</artifactId>
        <version>3.0.1.Final</version>
    </dependency>
```

Considere as seguintes classes:

```java
import javax.inject.Inject;

public class Controller {

    @Inject
    private Repositorio repositorio;

    public void greet() {
        System.out.println(repositorio.hello());
    }

}

import javax.inject.Inject;

public class Repositorio {

    @Inject
    private Dao dao;

    public String hello() {
        return dao.hello();
    }

}

public class Dao {

    public String hello() {
        return "Hello World!";
    }
}
```

Para injeta-las, precisamos inicializar nossa aplicação da seguinte forma:

```java
import org.jboss.weld.environment.se.Weld;
import org.jboss.weld.environment.se.WeldContainer;

public class Application {

    public static void main(String[] args) {
        Weld weld = new Weld();
        WeldContainer container = weld.initialize();

         Controller controller = container.select    (Controller.class).get();
        controller.greet();

        container.shutdown();
    }
}
```

Note que, usar o Weld fora do Application Server, algumas funcionalidades alteram-se principalmente os contextos dos beans. Veja mais na [documentação](http://docs.jboss.org/weld/reference/latest-master/en-US/html/environments.html#weld-se).

## Injeção de Dependências e Inversão de Controle

### Injetando uma Instância

Podemos injetar qualquer Bean utilizando a anotação `@Inject`.

```java
@WebServlet(name = "EmpregadoServlet", urlPatterns = {"",   "/empregados"})
public class EmpregadoServlet extends HttpServlet {

    @Inject
    private Cliente cliente;

    // More code
}
```

Note que se os Beans injetados possuirem dependências, estas também serão satisfeitas e é isto que faz com que a inversão de controle aconteça: quem controla a construção de instâncias agora é o Servidor de Aplicação / Container.

### Configurando Beans: Produtores

Para transformar objetos que não sejam de fácil instanciação em um `Managed Bean`, podemos utilizar uma anotação especial, a `@Produces`. Esta anotação define um `Factory Method` para um tipo, possibilitando que o Container obtenha uma instância para injeção quando necessário. Um exemplo bastante comum é a criação de um `Factory Method` para criar um `EntityManager`:

```java
public class EMFactory {

    private EntityManagerFactory emf;

    public EMFactory() {
        this.emf = Persistence.createEntityManagerFactory("pessoal-pu");
    }

    @Produces
    public EntityManager produzEntityManager() {
        return emf.createEntityManager();
    }
}
```

### Alterando Beans em Runtime

Algumas vezes, precisamos manter no ClassPath mais de um Bean de tipo similar \(implementam a mesma interface ou extendem uma mesma classe\), gerando uma ambiguidade: o container não saberá qual Bean injetar se este for requisitado através da Abstração. Para decidir entre estes diferentes Beans, temos algumas opções.

#### Produces

A primeira forma é utilizar uma método produtor \(`@Produces`\) que implementa a lógica necessária para alternar entras as instâncias. Note que, se o Bean produzido pelo método `@Produces` também estará disponível no container, o que poderá causar a mesma ambiguidade. Para corrigi-la, podemos marcar o Bean original como ignorado \(usando `@Vetoed`\). O problema é que, ao ignorar, as injeções do componente também não será processadas, o que poderá causar erros. A solução então é juntar este método com o próximo: qualificadores customizados.

#### Usando Qualificadores

Um qualificador customizado é uma anotação criada para dicionar uma qualificação à um bean. Podemos, por exemplo, criar anotações `@Production` e `@Development` e, ao injetar uma classe, podemos usar estes qualificadores para obtermos a classe correta:

Poderíamos, por exemplo, criar uma anotação chamada `@Outro` da seguinte forma:

```java
import javax.inject.Qualifier;
import java.lang.annotation.Retention;
import java.lang.annotation.Target;

import static java.lang.annotation.ElementType.*;
import static   java.lang.annotation.RetentionPolicy.RUNTIME;

@Qualifier
@Retention(RUNTIME)
@Target({TYPE, METHOD, FIELD, PARAMETER})
public @interface Outro {}
```

E marcariamos nosso repositório:

```java
@Outro
public class DepartamentoRepositorio2 implements DepartamentoRepositorio { ... }
```

Feito isto, poderíamos injetar este bean da seguinte forma:

```java
@Inject @Outro private DepartamentoRepositorio repositorio
```

#### Alternative

Adicionalmente, há uma anotação chamada `@Alternative` que permite que configuremos qual implementação será utilizada.

Para usa-la, marcamos o Bean com a anotação:

```java
@Alternative
public class TestCoderImpl implements Coder { ... }
```

Podemos ter um outro bean marcado como Default.

```java
public class CoderImpl implements Coder { ... }
```

E seu uso, através de injeção.

```java
@Inject Coder coder;
```

Para decidir qual bean utilizar podemos configurar no arquivo `beans.xml`.

encoder.TestCoderImpl

Alternar a classe indicada no arquivo `beans.xml` irá alterar a classe que será injetada.

#### Specializes

Uma outra opção é utilizar a anotação `@Specializes`. Quando existem dois beans disponíveis para uso, o marcado com a anotação `@Specializes` será sempre injetado, ou seja, irá sobrescrever o outro totalmente. O único detalhe é que o bean especializado deverá extender o outro bean, sendo realmente uma especialização.

```java
@Default @Asynchronous
public class AsynchronousService implements Service {   ... }

@Specializes
public class MockAsynchronousService extends    AsynchronousService { ... }
```

#### Alterando em tempo de Construção

Podemos alternar implementações no `pom.xml` para alternar os beans, afinal, estamos tirando uma implementação \(em um módulo\) do classpath e adicionando outra - considerando que ambas implementem uma abstração em comum, o CDI saberá como injetar a instância correta.

Poderiamos alternar

br.com.mobicarejpa-persistencia1.0-SNAPSHOTorg.hibernatehibernate-core

Por:

br.com.mobicaremmdb-persistencia1.0-SNAPSHOT

Como ambos os módulos incluem classes repositório que implementam as mesmas interfaces, uma aplicação que os consuma continuará funcionando.

### Excluindo Beans

Podemos excluir determinados beans do scan do Servidor de Aplicação. Isto é feito através da anotação `@Vetoed`.

```java
@Vetoed
@Entity
@Table(name = "departamento")
class DepartamentoJpa {

    @Id
    @GeneratedValue
    private Long id;

    @Column(nullable = false)
    private String nome;

    // More code
}
```

Outra forma de utilizar esta anotação é antes da declaração de package, fazendo com que toda as classes da package seja excluída do scan.

```java
@Vetoed
package br.com.mobicare.persistencia.jpa;

import br.com.mobicare.core.pessoal.Departamento;

import javax.enterprise.inject.Vetoed;
import javax.persistence.*;
import java.util.Collection;

@Vetoed
@Entity
@Table(name = "departamento")
class DepartamentoJpa {

    // Some code

}
```

Antes da versão 1.2 do CDI, para excluir, era necessário adicionar anotações no `beans.xml`.

## Inteceptadores

Interceptadores são um recurso antigo do Java que foi melhorado com a especificação CDI. Interceptadores fazem exatamente o que o nome sugere: interceptam chamadas à clases específicas e executam ações antes e/ou depois, assim como são os filtros em diversas bibliotecas Web.

Para criar um novo interceptador, devemos criar uma anotação da seguinte forma:

```java
@InterceptorBinding
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Transactional {   }
```

Com esta anotação em mãos, podemos anotar uma classe ou apenas um método.

```java
package com.miguelmf.study.weld;

public class Dao {

    @Transactional
    public Object save() {
        // Code
    }
}
```

Feita esta configuração, quando o método Save receber alguma chamada, o container irá procurar pela classe associada à anotação `@Transactional`. Podemos implementa-la da seguinte forma:

```java
@Transactional @Interceptor
public class TransactionInterceptor {

    @AroundInvoke
    public Object log(InvocationContext ctx) throws     Exception {

        System.out.println("Iniciando a execução do método  [" + ctx.getMethod().getName() + "]");

        Object result = ctx.proceed();

        System.out.println("Execução do método [" +     ctx.getMethod().getName() +
                "] finalizada, com valor [" + result + "]" );

        return result;
    }

}
```

Para que o Interceptador seja considerado ativo, devemos alterar também o arquivo `beans.xml`, adicionando o elemento

com.miguelmf.study.weld.interceptors.TransactionalInterceptor

O método log anotado com `@AroundInvoke` será invocado antes de cada chamada ao método `save`. Ao chamar o método `ctx.proceed` nós indicamos que o próximo interceptador deve ser executado. É obrigatório que:

1. O método `ctx.proceed` seja invocado;
2. Um `Object` seja retornado do método;

O valor de retorno \(o `Object`\) é o valor que será passado como retorno do método, portanto, devemos ter cuidado com edições.

A seção de código antes da chamada ao método `ctx.proceed` será executada antes do método ser invocada, e a seção posterior, após.

### Adicionando mais informações

Podemos adicionar mais informações à interceptadores através da adição de métodos membros da interface.

```java
@InterceptorBinding
@Target({METHOD, TYPE})
@Retention(RUNTIME)
public @interface Transactional {
   boolean requiresNew() default false;
}
```

Ao implementar esta anotação, poderemos definir um valor inicial para este atributo.

```java
@Transactional(requiresNew = true) @Interceptor
public class RequiresNewTransactionInterceptor {
   @AroundInvoke

   public Object manageTransaction(InvocationContext ctx)   throws Exception { ... }
}
```

feito isto, podemos ser explícitos que queremos um interceptador onde requiresNew seja `true`

```java
@Transactional(requiresNew = true)
public class ShoppingCart { ... }
```

No caso da existência de apenas um Bean, podemos configurar para que o CDI ignore esta chave com a anotação `Nonbinding`

```java
@InterceptorBinding
@Target({METHOD, TYPE})
@Retention(RUNTIME)
public @interface Secure {
   @Nonbinding String[] rolesAllowed() default {};
}
```

### Bind de diversos interceptadores

Podemos efetuar o binding de diversos interceptadores com uma classe acumulando as anotações.

```java
@Transactional
@Normalized
public class Dao {

    public Object save() {
        // Code
    }
}
```

Esta anotação fará com que ambos os interceptadores sejam executados. Podemos também utilizar a composição de interceptadores, associando-os uns aos outros.

Na definição da anotação, podemos efetuar a associação de outros interceptadores.

```java
@Transactional
@InterceptorBinding
@Target({ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
public @interface Normalized { }
```

Agora, ao anotar uma classe com `@Normalized` ambos os interceptadores serão executados.

```java
@Normalized
public class Dao {
    // Code
}
```

### Associações Adicionais

Um interceptador pode ser associado à dois tipos de pontos de interceptação: chamadas à métodos de negócio e pontos do ciclo de vida de um Bean - os exemplos que vimos anteriormente, referem-se ao grupo "Métodos de Negócio". Os Lifecycle Callbacks são métodos que devem ser executads quando um Bean atinge determinado ponto em seu ciclo de vida e, quando isto ocorrem, o container dispara um evento. Os eventos aos quais podemos associar interceptors são:

* Post-construct
* Pre-destroy
* Pre-passivate \(stateful session beans apenas\)
* Post-activate \(stateful session beans apenas\) 

Podemos implementar o interceptor `Normalized` na classe `NormalizedInterceptor` para exemplificar o uso destes callbacks.

```java
@Transactional @Interceptor
public class NormalizedInterceptor {

    @AroundInvoke
    public Object normalize(InvocationContext ctx) throws   Exception {

        System.out.println("<NormalizedInterceptor> -   Iniciando a execução do método [" + ctx.getMethod()   .getName() + "]");

        Object result = ctx.proceed();

        System.out.println("<NormalizedInterceptor> -   Execução do método [" + ctx.getMethod().getName() +
                "] finalizada, com valor [" + result + "]" );

        return result.toString().toUpperCase();
    }

    @PostConstruct
    public void alertConstruct(InvocationContext ctx) {
        System.out.println("POST CONSTRUCT");
    }

    @PreDestroy
    public void alertDestroy(InvocationContext ctx) {
        System.out.println("POST DESTROY");
    }

}
```

Este interceptador irá imprimir as mensagens "POST CONSTRUCT" sempre que um Bean anotado com `NormalizedInterceptor` for instanciado e "POST DESTROY" quando for destruído.

Fonte: [Documentação Jboss](http://docs.jboss.org/weld/reference/latest/en-US/html/interceptors.html)

## Decorators

Interceptadores não possuem conhecimento de qual operação ou objeto estão associados e, enquanto são perfeitos para tratar questões de infraestrutura ou detalhes técnicos \(como logs\) de forma geral no sistema, atingindo qualquer método ou classe anotada adequadamente.

No entanto, eles não são adequados para adicionar detalhes relacionados à negócio. Muitas vezes queremos adicionar algum tipo de funcionalidade à todas classes associadas à uma abstração específica \(interface\) e com conhecimento de qual operação está sendo executada e, para isto, os Interceptadores não podem nos ajudar.

Já os Decorators são perfeitos para este tipo de necessidade. Assim como o Pattern Decoretor, um Decorator do CDI é uma classe que implementa a mesma abstração que seus alvos e as encapsula, conhecendo a estrutura da abstração e adicionando funcionalidade onde necessário.

Considere a interface `Account`:

```java
public interface Account {

   public BigDecimal getBalance();
   public User getOwner();
   public void withdraw(BigDecimal amount);
   public void deposit(BigDecimal amount);

}
```

Podemos criar um Decorator para ela, implementando a mesma interface em uma classe anotada com `@Decorator`. Um Decorator _pode_ ser uma classe abstrata, o que nos permite não implementar os métodos que não receberão nenhum tipo de tratamento.

```java
@Decorator
public abstract class LargeTransactionDecorator implements  Account {
   @Inject @Delegate @Any Account account;

   @PersistenceContext EntityManager em;
   public void withdraw(BigDecimal amount) {
      account.withdraw(amount);
      if ( amount.compareTo(LARGE_AMOUNT)>0 ) {
         em.persist( new LoggedWithdrawl(amount) );
      }
   }

   public void deposit(BigDecimal amount);
      account.deposit(amount);
      if ( amount.compareTo(LARGE_AMOUNT)>0 ) {
         em.persist( new LoggedDeposit(amount) );
      }
   }
}
```

Um aspecto importante do Decorator é o atributo anotado com `@Delegate`. Ele é uma referência ao Bean Decorado e nos permite \(diferente do `InvocationContext.proceed`\) chamar _qualquer_ operação no objeto Decorado.

Fonte: [Documentação Jboss](http://docs.jboss.org/weld/reference/latest/en-US/html/decorators.html)

### Habilitando Decorators

Um decorator pode ser habilitado de duas maneiras.

A primeira é adicionando a seguinte linha no arquivo `beans.xml`, o que o ativará para apenas para o seu módulo\(Jar\):

org.mycompany.myapp. LargeTransactionDecorator

A segunda forma é utilizando a anotação `@Priority` que irá liga-lo globalmente. Esta anotação precisa de um valor inteiro que irá definir a prioridade \(maior == mais prioridade\)

```java
@Priority(value = 10)
@Decorator
public class LoggingDecorator implements DaoI {
    @Inject @Delegate @Any DaoI dao;

    @Override
    public String hello() {
        System.out.println("Logging Decorator");
        return dao.hello();
    }
}
```

Em casos em que mais de um Decorador for um match para uma classe, os anotados com `@Priority` serão executados primeiro e, depois, a ordem do arquivo `beans.xml` será seguida.

_Nota_: não habilite um Decorator no arquivo `beans.xml` e o anote com `@Priority` simultâneamente.

## Events

Eventos vão um passo à frente no desacoplamento provido pela injeção de dependências. Ao utilizar este recurso, beans podem interagir entre si sem qualquer tipo de dependência em tempo de compilação.

O esquema padrão de eventos é similar ao Pattern Observer/Observable, no entanto, com algumas mudanças.

* Tanto o lado Observer quanto o lado Observable ficam totalmente desacoplados um do outro;
* Observadores podem especificar uma combinação de "Seletores" para filtrar os eventos que recebem;
* Observadores podem ser notificados imediatamente ou podem especificar a chegada de um evento pode ser atrasada até o fim da transação atual;

### Declarando um evento

Um evento é um Qualifier e é declaro de maneira similar.

```java
@Qualifier
@Target({METHOD, FIELD, PARAMETER, TYPE})
@Retention(RUNTIME)
public @interface Updated { }
```

### Observadores

Um método observador é um bean com um paràmetro anotado com `@Observes`. Qualquer outro qualificador utilizado neste método, como o `Updated` que definimos, será usado para filtrar os eventos que este receberá.

```java
public void afterDocumentUpdate(@Observes @Updated Document document) { ... }
```

Um método observador pode possuir paràmetros adicionais, que também serão pontos de injeção.

```java
public void afterDocumentUpdate(@Observes @Updated Document document, User user) { ... }
```

Se um método observador não especificar nenhum qualificador de evento, este será notificado de todos os eventos ocorridos ao tipo observado.

### Produtores de Eventos

Produtores de eventos disparam eventos utilizando uma instância da Interface `Event`, que é obtida por injeção.

```java
@Inject @Any Event<Document> documentEvent;
@Inject @Updated Event<Document> anotherDocumentEvent;
```

Com estas instâncias, executamos os eventos com o método `fire`:

```java
documentEvent.fire(document);
```

O uso do Qualifier ao injegar estas instâncias na classe irá filtrar os eventos obtidos. Outra forma de filtrar os eventos é chamar o método `select` da interface `Event` e passar as classes dos Qualifiers.

```java
documentEvent.select(new AnnotationLiteral<Updated>(){}).fire(document);
```

Anotar o ponto de injeção é mais simples mas, para casos em que queremos requisitar eventos dinâmicamente, o `select` é a opção. Ambas as opções podem ser usadas simultâneamente.

### Qualificadores de evento com campos membro

Podemos adicionar campos membro à qualificadores para outra camada de 'filtro' dos eventos.

```java
@Qualifier
@Target({METHOD, FIELD, PARAMETER, TYPE})
@Retention(RUNTIME)
public @interface Role {
   RoleType value();
}
```

Podemos agora definir um observador para um Role de valor específico.

```java
public void adminLoggedIn(@Observes @Role(ADMIN) LoggedIn event) { ... }
```

Ao injetar os eventos, anotamos com o mesmo qualificador.

```java
@Inject @Role(ADMIN) Event<LoggedIn> loggedInEvent;
```

Também é possível faze-lo programaticamente mas precisamos primeiramente de uma subclasse de `AnnotationLiteral`

abstract class RoleBinding extends AnnotationLiteral implements Role {}

E então, passamos uma instância desta classe no método `Select`.

```java
documentEvent.select(new RoleBinding() {
   public void value() { return user.getRole(); }
}).fire(document);
```

### Observadores Transacionais

Podemos definir quando a notificação de um evento deve ser entregue à um observador. No exemplo abaixo, configuramos o observador coo `@Observes(during = AFTER_SUCCESS`, o que indica que este observador será chamado apenas quando a transação estiver completa e somente se for completa com sucesso.

```java
public void refreshCategoryTree(@Observes(during = AFTER_SUCCESS) CategoryUpdateEvent event) { ... }
```

Temos outros hooks:

| Transação | Descrição |
| :--- | :--- |
| IN\_PROGRESS | É o comportamento padrão, chama o observador imediatamente |
| AFTER\_SUCCESS | Na fase "Post Completion" da transação e somente se esta for bem sucedida |
| AFTER\_FAILURE | Na fase  "Post Completion" da transação e somente se esta falhar |
| AFTER\_COMPLETION | Na fase "Post Completion" da transação, independente de sucesso ou falha |
| BEFORE\_COMPLETION | Na fase "Before Completion" de uma transação, independente de sucesso ou falha |

Um exemplo de uso é em atualizações de dados:

```java
@Stateless
public class ProductManager {

   @PersistenceContext EntityManager em;
   @Inject @Any Event<Product> productEvent;

   public void delete(Product product) {
      em.delete(product);
      productEvent.select(new AnnotationLiteral<Deleted>(){}).fire(product);
   }

   public void persist(Product product) {
      em.persist(product);
      productEvent.select(new AnnotationLiteral<Created>(){}).fire(product);
   }
   ...
}
```

[http://docs.jboss.org/weld/reference/latest/en-US/html/events.html](http://docs.jboss.org/weld/reference/latest/en-US/html/events.html)

## Stereotypes

[http://docs.jboss.org/weld/reference/latest/en-US/html/stereotypes.html](http://docs.jboss.org/weld/reference/latest/en-US/html/stereotypes.html)

## Resources

São atributos de classes que são produtores de beans.

```java
@Produces @PersistenceContext(unitName="CustomerDatabase")
@CustomerDatabase EntityManager     customerDatabasePersistenceContext;
```

A vantagem é não ter de criar um método produtor. Diversos outros serviços tambẽm são suportados: `@Resource`, `@EJB`, `@PersistenceContext`, `@PersistenceUnit` or `@WebServiceRef`.

```java
@Produces @WebServiceRef(lookup="java:app/service/Catalog")
Catalog catalog;

@Produces @Resource(lookup="java:global/env/jdbc/CustomerDatasource")

@CustomerDatabase Datasource customerDatabase;
@Produces @PersistenceContext(unitName="CustomerDatabase")

@CustomerDatabase EntityManager     customerDatabasePersistenceContext;

@Produces @PersistenceUnit(unitName="CustomerDatabase")
@CustomerDatabase EntityManagerFactory  customerDatabasePersistenceUnit;

@Produces @EJB(ejbLink="../their.jar#PaymentService")
PaymentService paymentService;
```

Fonte: [Documentação Jboss](http://docs.jboss.org/weld/reference/latest/en-US/html/resources.html)

## Escopos

Podemos marcar um Bean \(ou um Factory Method\) com diferentes anotações que indicam o seu ciclo de vida. Uma nova instância é provida à um cliente sempre que o ciclo de vida da instância anterior chega ao fim. Os escopos disponíveis são:

| Escopo | Anotação | Duração |
| :--- | :--- | :--- |
| Request | @RequestScoped | Uma interação com uma aplicação Web em um request HTTP. |
| Session | @SessionScoped | Uma interação com uma aplicação Web através de diversos requests HTTP. |
| Application | @ApplicationScoped | Estado compartilhado entre todas as interações do usuário com uma aplicação web. |
| Dependent | @Dependent | É o escopo default e significa que a instância existe para servir exatamente um bean e possui o mesmo ciclo de vida que o bean cliente. |
| Conversation | @ConversationScoped | Uma interação do usuário com um JavaServer Faces Application \(JSF\), com um escopo controlado explicitamente pelo desenvolvedor, que pode ou não atravessar diversas invocações do ciclo de vida JSF. |

Exemplo de uso:

```java
import javax.inject.Inject;
import javax.enterprise.context.RequestScoped;

@RequestScoped
public class Printer {
    @Inject @Informal Greeting greeting;

    // More code
}
```

_Nota_: ao usar o Weld ou outra implementação CDI em aplicações Java SE, os escopos são diferentes.

## Injeção de Dependências e Testes

### Testes de Unidade \(Junit e Mockito\)

Classes que utilizem injeção de dependências também podem ser testadas utilizando o `Mockito`. Como, geralmente, a injeção não ocorre no construtor \(mas pode\) e sim em atributos, o Mockito possui funcionalidade específica para identificar quais atributos precisam receber uma instância. Considere a classe abaixo:

```java
public class ClienteApi implements Cliente {

    @Inject
    private DepartamentoRepositorio departamentoRepositorio;

    @Inject
    private EmpregadoRepositorio empregadoRepositorio;

    // More code
}
```

Para testar esta classe, precisamos de instâncias Mockadas de `DepartamentoRepositorio` e `EmpregadoRepositorio`. Como a classe não recebe estes instâncias via construtor, deve-se utilizar um recurso específico do Mockito para injeta-las.

```java
@RunWith(MockitoJUnitRunner.class)
public class ClienteApiTest {

    @Mock
    private DepartamentoRepositorio departamentoRepositorio;

    @Mock
    private EmpregadoRepositorio empregadoRepositorio;

    @InjectMocks
    private ClienteApi cliente = new ClienteApi();

    // More tests
}
```

Usando a anotação `@InjectMocks` no objeto `ClienteApi` fará com Mockito procure por pontos de injeção \(`@Inject`\) e, caso encontre, irá injetar os Mocks que se adequem aos tipos requisitados nos pontos de injeção \(no caso, `DepartamentoRepositorio` e `EmpregadoRepositorio`\). Caso não existam Mocks, os testes iráo resultar em erros.

### Testes de Integração

Nos testes de integração, instâncias reais devem ser injetadas em objetos, fazendo com que a funcionalidade `@InjectMocks` do Mockito não mais nos atenda.

#### Reflection

Podemos acessar os atributos de uma instância via Reflection _mesmo que este seja privado_. Este artifício é muito utilizado por Frameworks/Libraries \(como o CDI\) e, quando usado com sabedoria, pode produzir bons resultados. No contexto de testes, iremos acessar a classe à ser testada e atribuir um valor à variável que deveria receber uma instância do Container Weld.

Para efetuar testes de integração em um módulo de persistência, tomemos como exemplo a classe `DepartamentoRepositorioJpa`. Esta classe precisa receber um `EntityManager` para executar as suas operações.

```java
public class DepartamentoRepositorioJpa implements DepartamentoRepositorio {

    @Inject
    private EntityManager em;

    // More code
}
```

Podemos então, em nossa classe de testes utilizar do seguinte artifício:

```java
@Before
public void setUp() throws Exception {
    departamentoRepositorio = new DepartamentoRepositorioJpa();
    empregadoRepositorio = new EmpregadoRepositorioJpa();

    injectEntityManager(departamentoRepositorio);
    injectEntityManager(empregadoRepositorio);
}


protected void injectEntityManager(Object object) throws NoSuchFieldException, IllegalAccessException {
    Field field = object.getClass().getDeclaredField("em");

    field.setAccessible(true);
    field.set(object, em);
    field.setAccessible(false);
}
```

Feito isto, os testes irão ocorrer normalmente. Uma dificuldade ao utilizar este método é que, no caso de classes complexas \(com muitas dependências\) torna-se trabalhoso e complicado setar manualmente dependências de todos os objetos envolvidos.

#### Container Weld

\[TD\] - Pesquisar e testar a possibilidade de configurar um Container Weld em escopo de testes;

#### Arquillian

Com o [Arquillian](http://arquillian.org/) \[TD\] - Criar projeto para testes/aprendizado de Arquillian \(Em outras palavras, eu não sei nada\)

