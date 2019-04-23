---
description: Java Persistence API é a especificação do Java EE para persistência de dados.
---

# JPA

## Introdução

Java Persistence API é a especificação do Java EE para persistência de dados. Contém diversas anotações que nos permitem armazenar dados em bancos de dados sem nem ao menos escrever SQL \(ok, nem sempre\).

## Conceitos

### Configuração do JPA

Para adicioanar o JPA à um projeto, precismaos como de costume buscar uma implementação concreta da especificação. A mais utilizada é o Hibernate.

```markup
    <dependency>
        <groupId>org.hibernate</groupId>
        <artifactId>hibernate-core</artifactId>
        <version>5.2.10.Final</version>
    </dependency>
```

Adicionalmente, para configurar o JPA, precisamos adicionar de um arquivo XML chamado `persistence.xml` ao `META-INF` do projeto.

```markup
    <?xml version="1.0" encoding="UTF-8"?>
    <persistence    xmlns="http://xmlns.jcp.org/xml/ns/persistence"    version="2.1">

        <persistence-unit name="jpa-pu">

            <provider>org.hibernate.jpa.HibernatePersistenceProvider</provider>

            <class>com.miguelmf.study.jpa.modelo.Pais</class>

            <properties>
                <property   name="javax.persistence.jdbc.driver" value="org.h2.Driver"/>
                <property name="javax.persistence.jdbc.url" value="jdbc:h2:./appdb"/>
                <property name="javax.persistence.jdbc.user" value="sa"/>
                <property   name="javax.persistence.jdbc.password" value=""/>

                <property name="hibernate.dialect" value="org.hibernate.dialect.H2Dialect"/>
                <property name="hibernate.show_sql" value="true"/>
                <property name="hibernate.hbm2ddl.auto" value="create-drop"/>
            </properties>
        </persistence-unit>

    </persistence>
```

Note que no arquivo de configuração adicionamos as propriedades da conexão com o banco de dados, de maneira idêntica ao que seria feito no arquivo de configuração do Hibernate.

O elemento `<persistence-unit>` é essencial e define o nome de uma unidade de persistência. Uma unidade de persistência é um dos conceitos principais do JPA e pode ser compreendido por um conjunto de classes que são associadas à um formato de persistência específico. Podemos listar ou não as classes que pertencem à unidade de persistência utilizando um elemento `<class>`.

### Entidades

Uma entidade JPA deve:

* Ser anotada com `@Entity`
* Deve possuir um construtor sem argumentos publico ou protected, mas pode possuir outros construtores.
* A class não deve ser `final` nem nenhum de seus atributos a serem persistidos.
* Se uma classe for passada como um objeto 'destacado' \(contido em outro objeto e requisitada persistência do objeto pai\), esta classe deve implementar a interface `Serializable`
* Atributos \(variáveis de instância\) devem ser declaradas como `private`, `protected` ou `package-private`, sendo acessíveis apenas através de métodos da classe.

Uma classe marcada como entidade terá todos os seus atributos persistidos a não ser que estejam marcados com `@Transient`.

Para definir uma chave primária, usamos a anotação `@Id`. Para chaves primárias complexas e compostas, geralmente usa-se uma classe que represente estes dados. Uma classe que represente uma chave primária deve:

* Ser pública
* Ter suas propriedades publicas ou protected.
* Ter um construtor default publico.
* Implementar `hashCode` e `equals`.
* Implementar a interface Serializable.

```java
    public final class LineItemKey implements Serializable {
        public Integer orderId;
        public int itemId;

        public LineItemKey() {}

        public LineItemKey(Integer orderId, int itemId) {
            this.orderId = orderId;
            this.itemId = itemId;
        }

        public boolean equals(Object otherOb) {
            // Code
        }

        public int hashCode() {
            // Code
        }

    }
```

### Multiplicidade

Existem quatro multiplicidades:

* One to One: representada pela anotação `@OneToOne`
* One to Many: representada pela anotação `@OneToMany`
* Many To One: representada pela anotação `@ManyToOne`
* Many to Many: representada pela anotação `@ManyToMany`

### Direção em relacionamentos

#### Relacionamentos Bidirecionais

Relacionamentos bidirecionais indicam que cada entidade envolvida nele sabe sobre a existência da outra parte. Em código Java, isto significa que duas classes que se relacionam possuem um atributo uma referenciando a outra. Neste caso é dito que um lado é o 'dono' e o outro é o lado inverso.

Para representar isto no Jpa, temos de seguir as seguintes regras:

* O lado inverso do relacionamento deve referenciar o lado dono usando o elemento `mappedBy` da anotação `OneToMany` ou `ManyToMany`.
* O lado `many` de um relacionamento `Many to One` bidirecional não deve definir um elemento `mappedBy` já que o lado `many` é sempre o dono do relacionamento.
* Em relacionamentos `One to One` bidirecionais o lado dono corresponde ao lado que contem uma chave estrangeira \(FK\).
* Para relacionamentos bidirecionais `Many to Many`, qualquer um dos lados pode ser o lado dono.

Para criar tabelas associativas \(Join Tables\) manualmente, temos que criar uma estrutura similar à:

```java
    // Entidade Bar
    @Entity
    @Table(name = "bar")
    public class BarJpa {

        @Id
        @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;

        private String nome;

        @OneToMany(mappedBy ="bar",
                cascade = CascadeType.ALL,
                fetch = FetchType.LAZY)
        private Collection<ProdutoBarJpa> produtos;
        // More Code

    }

    // Tabela associativa Bar x Produto
    @Entity
    @Table(name = "produto_bar")
    public class ProdutoBarJpa {

        @EmbeddedId
        private ProdutoBarCompositePk id;

        @ManyToOne
        @PrimaryKeyJoinColumn(name = "barId", referencedColumnName = "id")
        private BarJpa bar;

        @ManyToOne
        @PrimaryKeyJoinColumn(name = "produtoId", referencedColumnName = "id")
        private ProdutoJpa produto;

        // More Code
    }

    // Tabela produto
    @Entity
    @Table(name = "produto")
    public class ProdutoJpa {

        @Id @GeneratedValue(strategy = GenerationType.IDENTITY)
        private Long id;
        private String nome;
        private Boolean aprovado;
        // More Code
    }
```

#### Relacionamentos Unidirecionais

Em um Relacionamento Unidirecional, apenas um dos lados sabe sobre a existência do outro, portanto, não existem necessidades especiais além da anotação do relacionamento.

### Cascade

É possível especificar os relacionamentos de cascading entre as tabelas, para restringir ou garantir que os registros associados sejam deletados.

```java
    @OneToMany(cascade=REMOVE, mappedBy="customer")
    public Set<Order> getOrders() { return orders; }
```

### Classes Abstratas

Classes abstratas podem ser entidades, o que fará com que queries operem sobre todas as suas subclasses.

### Superclasses mapeadas

É possíveis criar uma superclasse que irá conter informações úteis para o mapeamento mas que não é uma entidade em si utilizando a anotação `@MappedSuperclass`. Estas classes não são persistidas e não podem ser referenciadas em queries.

```java
    @MappedSuperclass
    public class Employee {
        @Id
        protected Integer employeeId;
        ...
    }
    @Entity
    public class FullTimeEmployee extends Employee {
        protected Integer salary;
        ...
    }
    @Entity
    public class PartTimeEmployee extends Employee {
        protected Float hourlyWage;
        ...
    }
```

## Gerenciando Entidades

### O EntityManager

O objeto `EntityManager` é o objeto principal para gerenciarmos as entidades persistidas. Podemos construir um da seguinte forma:

```java
    EntityManagerFactoryemf = Persistence.createEntityManagerFactory("jpa-pu");
    EntityManager em = emf.createEntityManager();
```

A String `"jpa-pu"` é o nome da unidade de persistência que estamos carregando. Quando a aplicação estiver em execução em um Application Server podemos requisitar um EntityManager da seguinte maneira:

```java
    @PersistenceContext(unitName="jpa-pu")
    EntityManager em;
```

Com o EntityManager em mãos, podemos utiliza-lo para interagir com o JPA.

```java
    em.find(MyClass.class, 1); // o 1 é um ID
```

Para utilizar uma transação:

```java
    em.getTransaction().begin();
    em.persist(brasil);
    em.persist(rioDeJaneiro);
    em.persist(rj);
    em.getTransaction().commit();
```

E para criar queries:

```java
    em.createQuery("from estado where pais.id = :paisid")
        .setParameter("paisid", brasil.getId())
        .getResultList()
        .forEach(System.out::println);
```

Note que as queries do JPA não são escritas em SQL e sim em `JPQL`.

## Notas de Arquitetura

### Separação de Responsabilidades

Ainda que o JPA baseie-se em anotações que, inicialmente, parecem evitar código relacionado ao banco de dados, estas não devem ser misturadas com o modelo de domínio. Este visão foi o que criou o conceito de Persistence Model, inspirado pelo DDD.

É normal que sejam criadas classes que refletem diretamente as estruturas de dados à serem persistidas, seguindo o padrão JavaBean, o que já as garante os requisitos necessários para serem [Entidades](jpa.md#entidades).

Entre as classes de domínio e as de persistência, funcionalidades de mapeamento deverão ser construídas.

## Referências

* Não há

