---
description: A menor estrutura possível para servir aplicações web com JEE Servlets.
---

# JEE Web App com Servlets

## Introdução

O Java dispõe do Java EE: um conjunto de especificações que resolvem problemas comuns de infraestrutura de aplicações. Dentre os problemas resolvidos por esta espeficação, está o de servir aplicações web. Este documento descreve a estrutura mais simples possível de uma aplicação web com [Servlets](https://pt.wikipedia.org/wiki/Servlet)

## Configuração

### Estrutura de Diretório

A estrutura de diretório para construção de uma aplicação web java empacotada como War, deve ser similar à:

```text
    src
    ├── main
    │   ├── java
    │   │   └── com
    │   │       └── miguelmf
    │   │           └── study
    │   │               └── jee
    │   │                   └── servlet
    │   │                       └── HelloWorldServlet.java
    │   ├── resources
    │   └── webapp
    │       ├── index.html
    │       └── WEB-INF
    │           ├── hello.html
    │           ├── jboss-web.xml
    │           └── web.xml
    └── test
        └── java
            └── com
                └── miguelmf
                    └── study
```

### Arquivo Web Xml

O arquivo `web.xml` deve existir para que o Application Server possa carregar a aplicação adequadamente.

```markup
    <?xml version="1.0" encoding="UTF-8"?>
    <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xmlns="http://java.sun.com/xml/ns/javaee"
             xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
             xsi:schemaLocation="http://java.sun.com/xml/ns/javaee
            http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
             id="WebApp_ID" version="2.5">

        <display-name>jee-servlets</display-name>

        <welcome-file-list>
            <welcome-file>index.html</welcome-file>
        </welcome-file-list>

    </web-app>
```

### Application Servers versus web.xml

Alguns Application Servers precisam que determinadas configurações sejam feitas de maneira específica como, por exemplo, o Wildfly. No Wildfly, devemos criar um arquivo `jboss-web.xml` para definir configurações, uma delas, o nome `root` da aplicação.

```markup
    <?xml version="1.0" encoding="UTF-8"?>
    <jboss-web xmlns="http://www.jboss.com/xml/ns/javaee"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="
            http://www.jboss.com/xml/ns/javaee
            http://www.jboss.org/j2ee/schema/jboss-web_5_1.xsd">
        <context-root>jee-servlets</context-root>
    </jboss-web>
```

### Hello World

Com a configuração no lugar, podemos criar o nosso primeiro servlet e acessa-lo através da URL `http://localhost:8080/jee-servlets/hello`, considerando é claro que seu servidor está rodando na porta 8080..

```java
    import javax.servlet.ServletException;
    import javax.servlet.annotation.WebServlet;
    import javax.servlet.http.HttpServlet;
    import javax.servlet.http.HttpServletRequest;
    import javax.servlet.http.HttpServletResponse;
    import java.io.IOException;

    @WebServlet(urlPatterns = {"/hello"})
    public class HelloWorldServlet extends HttpServlet {

        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws   ServletException, IOException {
            req.getRequestDispatcher("WEB-INF/hello.html").forward(req, resp);
        }
    }
```

## Referências e links

* [Wikipedia - Java Servlets](https://pt.wikipedia.org/wiki/Servlet)
* [Caelum - Curso Java para Desenvolvimento Web: O que é Java EE](https://www.caelum.com.br/apostila-java-web/o-que-e-java-ee/)

