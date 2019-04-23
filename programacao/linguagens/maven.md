---
description: O sistema de build mais utilizado em aplicações java.
---

# Maven

## Introdução 
Maven é uma ferramenta de automação de construção. Ela automatiza o processo de build de aplicações Java e gerenciamento de dependências. O maven possui a capacidade de acessar um repositório e efetuar os downloads das dependẽncias em nosso projeto. Adicionalmente, é possível gerar documentação como Javadocs, documentações, reports e changelogs.

## Criando um projeto com o Maven
Para criar um projeto com maven, usamos a ferramenta de linha de comando mvn da
seguinte forma:


    mvn archetype:generate 
      -DgroupId=com.miguelmf.mvn 
      -DartifactId=mvn-playground 
      -DarchetypeArtifactId=maven-archetype-quickstart 
      -DinteractiveMode=false


Este comando irá criar um novo projeto chamado mvn-playground no diretório de trabalho atual. Chamar `mvn archetype:generate` sem parâmetros irá fazer com que o maven incie o modo interativo, onde o usuário terá a oportunidade de selecionar as opções respondendo à perguntas.

Para efetuar o build de um projeto com o Maven, usamos o comando

    mvn package

## Maven Lifecycles
O maven possui uma série de Lifecycles composto de phases. Os principais são clean, default e site. Um plugin, é uma coleção de Goals. Goals são as ações executadas pelo Maven sobre o projeto. Package é uma fase (phase) e não um goal. É um passo no ciclo de build, que é uma sequência ordenada de fases. Este comando irá executar as seguintes phases:

1. validate
2. generate-sources
3. process-sources
4. generate-resources
5. process-resources
6. compile

Após o término do build, podemos testar a aplicação com o seguinte comando:

    java -cp target/my-app-1.0-SNAPSHOT.jar com.mycompany.app.App

### Maven Phases e Lyfecycles
Uma lista incompleta das fases maven:

- validate
- compile
- test
- package
- integration-test
- verify
- install
- deploy

E seus lifecycles:

- default
- clean
- site

Fases são mapeadas para goals (um goal é contido em uma fase). Os goals específicos que serão executados por fase depende do tipo de packaging do projeto. Por exemplo, o comando package executa `jar:jar` se o tipo do projeto é Jar e `war:war` se o projeto for war.

## Diretórios
O maven possui uma série de diretórios padrão onde ele irá buscar pelos arquivos da aplicação [3]. No entanto, é possível configurar alguns destes diretórios para outros locais, garantindo compatibilidade com aplicações antigas. As configurações estão presentes na definição do arquivo Pom [4].

## Herança
Os arquivos Pom.xml herdam configurações de um arquivo pom chamado superpom. No
entanto, podemos definir de qual arquivo pom desejamos herdar.

``` xml
    <parent>
        <artfactId>organization.parent-pom</artifactId>
        <groupId>com.infiniteskill.maven</groupId>
        </version>1.0.0</version>
    </parent>
```

## Profiles
Esta funcionalidade permite que configuremos um conjunto de configurações sob um
nome como, por exemplo, produção, desenvolvimento e teste.

``` xml
    <profiles>
        <profile>
            <id>Production</id>
        </profile>
    </profiles>
```

Dentro do tag profile, pode-se utilizar quase qualquer recurso do maven (Ex:
especificar um diretório de output, dependências, etc)

Para invocar o mvn especificando um profile usamos o comando da seguinte forma:

    mvn -Pproduction package

Para os casos em que não podemos utilizar a linha de comando, o maven possui um
recurso chamado activators.

Activators são definições de critérios à considerar no momento do build que,
quando atendidos, ativam um profile específico. 

``` xml
    <profiles>
        <profile>
            <id>Production</id>
            <activation>
              <property>
                <name>env.PACKAGE_ENV</name>
                <value>PROD</value>
              </property>
            </activation>
        </profile>
    </profiles>
```

Com a configuração, o maven irá buscar dentro das variáveis de ambiente, uma
variável PACKAGE_ENV e se seu valor for PROD, executar o profile Production.

-- Dependências
O Maven possui um mecanismo de gestão de depêndencias extremamente útil. Devemos
adicionar as dependências dentro do bloco dependencies do XML.

``` xml
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
```

Dependências podem ser buscadas do repositório central do Maven. Para baixar
dependências especificadas no arquivo pom.xml, usamos o comando:

    mvn dependency:copy-dependencies

## Escopo de Dependências
Cada dependencia adicionada no arquivo pom.xml possui um escopo associado. Caso
não seja informano no arquivo, o escopo default é o compile.

Existem 6 escopos:

- compile
  - Disponível durante o ciclo de teste e compilação.
- test
  - Disponível durante o ciclo de testes
- import
- runtime
  - Uma dependência é necessária quando a aplicação for executada ou testada,
    mas não durante a compilação.
- system
  - A dependência será provida pelo sistema de arquivos, ou seja, indica-se um
    diretório onde o JAR está e este será copiado. Não recomendado.
- provided
  - A dependência estará disponível pelo JDK ou um Container

## Conflitos entre dependências
Quando dependências causam um conflito, o maven tenta identificar qual a versão
mais recente para incluir no Classpath.

No entanto, podemos facilitar o trabalho excluindo uma dependência transitiva da
seguinte forma.

``` xml
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
      <exclusions>
        <exclusion>
          <groupId>org.apache.commons</groupId>
          <artifactId>commons-lang3</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
  </dependencies>
```

Desta forma, o Maven irá excluir a dependência indicada, evitando o conflito. O maven irá indicar quando um conflito acontecer, às vezes. Para analisar por conta própria os possíveis problemas, existem dois comandos interessantes:

- dependency:analyze: gera um relatório das dependências usadas mas não declaradas e não usadas mas declaradas, facilitando o ajuste do arquivo pom.xml.
- dependency:tree: gera uma representação em formato de árvore das dependências do projeto, exibindo também as dependências transitivas.

## Maven lifecycles, phases e goals
Um lifecycle é um conjunto de passos ou estágios que passamos quando efetuamos o build de um artefato. EStes passos ou estágios são chamados de phases.

Os lifecycles padrão do maven são: default, clean e site.

Cada phase possui um ou mais goals associados. Um Goal é uma ação que será
executada durante o phase.

Quando executamos o comando maven como o abaixo, estamos executando uma phase.

    mvn clean

Neste caso, o maven irá identificar no lifecycle, todas as phases anteriores e
as executar também. Para verificar os dados de uma phase, podemos usar o comando:

    mvn help:describe -Dcmd=deploy

De acordo com a configuração de packaging do projeto (Ex: Jar) o maven utiliza os plugins corretos para montar o projeto.

## Principais Phases
### Compile
Compila o código fonte Java em arquivos class

### Test-Compile
Compila o código fonte dos arquivos de teste

### Test
Executa os arquivos de teste

### Package
Busca o código compilado e o empacota no formato de distribuição indicado no
arquivo pom.xml

### Install
Instala o package no reposistório local, para uso como uma dependência em outros
projetos localmente.

### Deploy
Copia o pacote final para um repositório final, compartilhando com outros
developers e projetos.


## Goals e Plugins
Goals executam tarefas sobre nosso projeto e produzem um resultado. Um plugin é
um agregador de Goals.

Para executar um plugin via linha de comando usamos:

    mvn compiler:compile

Compile é um plugin e compile é um goal.

Plugins como o compile estão definidos no superpom.xml. Podemos obter
informações sobre um plugin com o comando:

    mvn help:describe -Dplugin=compiler

O próprio plugin possui um goal help que podemos acessar:

    mvn  compiler:help -Ddetail=true -Dgoal=compile

Com estas informações, podemos adicionar mais configurações ao nosso pom.xml e
alterar o nosso procedimento de construção da codebase.

## Propriedades de um Plugin
A maioria dos plugins permite uma grande quantidade de configurações para
atender diversos tipos de projeto. Para descobrirmos quais são as propriedades
disponíveis, usamos:

    mvn help:describe -Dcmd=compiler:compile -Ddetail

Nós podemos configurar estas propriedades através da linha de comando

    mvn compiler:compile -Dmaven.compiler.verbose=true

Outra forma de especificar estas propriedades é através do arquivo pom.xml, que
é o melhor formato. Para isto, usamos a chave build.

``` xml
    <build>
      <pluginManagement>
        <plugins>
          <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.2<version>
            <configuration>
              <verbose>true</verbose>
            </configuration>
          </plugin>
        </plugins>
      </pluginManagement>
    </build>
```

O plugin acima está presente no superpom.xml e, por isto, podemos simplesmente
configura-lo no elemento <pluginManagement>. No caso de um plugin que não
esteja definido no superpom.xml, basta adiciona-lo no elemento build antes de
configura-lo no elemento <pluginManagement>.

## Plugins Customizados
Podemos criar novos plugins, caso não encontremos um que faça o que precisamos
(raro). Há um archetype para auxiliar este procedimento.

    mvn archetype:create 
      -DgroupId=com.infiniteskills.maven
      -DartifactId=first-custom -DarchetypeArtifactId=maven-archetype-mojo
      -DarchetypeGroupId=org.apache.maven.archetype

Este comando irá criar um projeto padrão para desenvolvermos nosso plugin.

Para configurarmos um prefixo no plugin para podermos chama-lo facilmente em
projetos que o utilizem, precisamos configura-lo.

No plugin, configuramos o prefixo do goal no pom.xml da seguinte forma:

``` xml
    <build>
        <plugins>
            <plugin>
                <artifactId>maven-plugin-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                    <goalPrefix>fCustom</goalPrefix>
                </configuration>
            </plugin>
        </plugins>
    </build>
```

No projeto que consome o plugin, efetuamos o bind do plugin com o phase compile
da seguinte forma:

``` xml
    <build>
        <plugins>
            <plugin>
                <groupId>com.infiniteskills.maven</groupId>
                <artifactId>first-custom</artifactId>
                <version>1.0-SNAPSHOT</version>
                <executions>
                    <execution>
                        <id>first-custom-compile</id>
                        <phase>compile</phase>
                        <goals>
                            <goal>touch</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
```

## Adicionando Repositórios
Podemos alterar os repositórios maven utilizados para obter dependências através da edição do arquivo de configuração central do maven.

No diretório de instalação do maven, devemos acessar o arquivo $INSTALL_DIR/Conf/settings.xml

``` xml
        <profile>
            <id>spring_remote</id>
            <repositories>
              <repository>
                <id>spring_repository</id>
                <url>http://repo.string.io/release/</url>
              </repository>
            </repositories>
        </profile>

        <activeProfile>
            <activeProfile>spring_remote</activeProfile>
        </activeProfiles>
```

Ao salvar estas alterações, o maven irá acessar o repositório do spring por
dependências.

## Principais Plugins
Existem muitos plugins disponíveis para o Maven[5]. Vamos verificar os principais.

### Clean
Não há muito o que ser dito aqui, o plugin clean simplesmente deleta o diretório
target.

### Jar
Invocado no phase Package, este plugin cria um pacote jar. Nós podemos invoca-lo
na linha de comando via

    mvn jar:jar

Existe uma grande quantidade de opções de configuração visíveis na documentação
ou via describe.

### Javadoc
Cria um Javadoc para seu projeto. O plugin possui muitos outros goals que podem
ser usados para gerar o javadoc em diversos formatos e, além disso, também
possui diversas propriedades como Header e Footer.

    mvn javadoc:javadoc -Dheader=MyPrj -Dfooter=Copyright

Como este plugin também pode ser adicionado ao processo de build, é interessante
a possibilidade de gerar o projeto com toda a sua documentação.

Um detalhe importante é que o plugin javadoc não está no superpom, por isso,
deve ser especificado no elemento <build> do pom.xml para, posteriormente, ser
configurado no elemento <pluginManagement.>

### Install e Deploy
O plugin Install simplesmente copia um projeto para o repositório maven local,
possibilitando que ele seja usado por outros projetos.

Já o plugin deploy, faz o deploy por um repositório externo como o maven
central repository. ESte plugin é útil para desenvolvedores de libraries como
para equipes de desenvolvimento internos em empresas, onde artefatos
desenvolvidos poderão ser automaticamente enviados para um repositório privado.

Para configurar um repositório externo, precisamos efetuar alterações no arquivo
pom.xml.

``` xml
     <distributionManagement>
        <repository>
          <id>RepoId</id>
          <name>repository-releases</name>
          <url>http://www.urltorepo.com/</url>
        </repository>
    </distributionManagement>   
```

Ao executar o procedimento de build(install phase), o maven efetuará o upload da aplicação
automaticamente.

### Surefire Plugin
Plugin responsável por executar os testes do projeto. A importância dos testes é
que eles impedem install e o deploy caso existam erros.

### Eclipse
Este plugin transforma o projeto em um projeto eclipse, criando os arquivos
necessários para que o eclipse consiga importar o projeto.

    mvn eclipse:eclipse

### War Plugin
Faz o empacotamento do projeto em um War. O bind do plugin pode ser feito no
phase package.

    mvn war:war

## Archetypes
São modelos de projetos pré construídos. Estes modelos oferecem grande agilidade
no início de projetos, evitando que configurações complexas sejam refeitas a
cada início de projeto.

Adicionalmente, grandes grupos de desenvolvedores como a Spring, construiu
modelos e os disponibilizou para uso geral.

Podemos criar um novo archetype criando um projeto Maven padrão.

    mvn archetype:generate

Após efetuar as configurações necessárias no projeto, executamos o goal de
criação de archetypes:

    mvn archetype:create-from-project

Isto irá transformar o projeto em um archetype, gerando os arquivos necessários
na pasta target. Após isto, deveremos navegar até a pasta
target/geenerated-sources/archetype e executar:

    mvn install

Isto irá instalar o archetype no repositório local, fazendo que que ele fique
disponível para a construção de outros projetos.

Para criar um novo projeto com este archetyoe, usamos:

    mvn archetype:generate

O archetype gerado será exibido na lista de archetypes.

-- Projetos com múltiplos módulos
Podemos criar um projeto separado em diversos módulos e gerencia-lo através do
maven.

Para isto, precisamos criar um projeto apenas para configuração. Este projeto,
deverá conter apenas um arquivo pom.xml, cuja diferença em sua configuração são
os dados abaixo.

``` xml
  <packaging>pom</packaging>

  <modules>
    <module>../maven-multimodule-module</module>
    <module>../maven-multimodule-module2</module>
  </module>
  ```

Ao executar o build deste projeto, o maven irá buscar os módulos e efetuar os
devidos procedimentos em cada um deles, utilizando um plugin chamado Reactor.

## Usando Containers e Servidores
Podemos configurar um servidor para que o Maven efetue o Deploy de uma
aplicação. Para isto devemos configurar um servidor no arquivo settings.xml do
maven.

No arquivo, devemos buscar o elemento server ou criar um da seguinte forma:

``` xml
    <server>
        <id>tomcat-server</id>
        <username>admin</username>
        <password>abc,123</password>
    </server>
```

Considerando que este servidor é um servidor tomcat, podemos adicionar no
arquivo pom.xml de um projeto que o utilize um plugin chamado maven-tomcat[8].

``` xml
      <plugins>
        <plugin>
          <groupId>org.apache.tomcat.maven</groupId>
          <artifactId>tomcat6-maven-plugin</artifactId>
          <version>2.2</version>
          <configuration>
            <url>http://www.localhost.com:8080/manager</url>
            <id>tomcat-server</id>
          </configuration>
        </plugin>
      </plugins>
```

Para efetuar o deploy, usamos o comando:

    mvn tomcat7:deploy

## Criptografia
Ao adicionar senhas aos arquivos de configuração do Maven, podemos colocar a
segurança de servidores em risco.

Para evitar esta falha de segurança, o maven permite o uso de critografia.

Para configurar isto, acesso o repositório local na pasta ~/.m2 e crie um
arquivo chamado settings-security.xml.

Através da linha de comando, eu seu projeto, execute o comando:

    mvn -emp <password>

O maven irá retornar um password master criptografado. Acesse agora o arquivo
settings-security.xml e adicione o seguinte conteúdo:

``` xml
    <settingsSecurity>
        <master>${password}></master>
    </settingsSecurity>
```

Então, agora, para criptografar um password que vá ser adicionado no arquivo
settings.xml do maven, podemos executar via linha de comando:

    mvn -ep <password>

O maven irá retornar um hash que é o valor que deverá ser adicionado no arquivo
settings.xml no lugar do password.

## Properties
Podemos criar propriedades dentro de um arquivo POM a fim de compartilhar
dados no arquivo e através de diversos projetos.

Nós podemos alterar o valor de qualquer elemento de arquivo POM por um token:

    ${project.artifactId}

Este token, no momento da execução do maven, será substituido pelo artifactId do
projeto.

Existem muitas possibilidades, de valores que podemos obter do ambiente e, mais
importante, podemos criar valores.

Criando uma propriedade chamada ${tomcat.url} pode ser feito dessa forma:

    <properties>
        <tomcat.url>http://localhost:8080/site</tomcat.url>
    </properties>

## Debug
Ao executar qualquer operação do Maven, podemos usar a chave de debug para obter
mais informações.

    mvn -X <commands>

## Referências e links
- [1] [Learning Apache Maven](https://www.safaribooksonline.com/library/view/learning-apache-maven/9781771373661/video212486.html)

- [2] [Filosofia Maven](https://maven.apache.org/background/philosophy-of-maven.html)

- [3] [Maven Default Directories](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)

- [4] [Definição do Arquivo POM](https://maven.apache.org/pom.html)

- [5] [Maven Plugins](https://maven.apache.org/plugins/)

- [6] [Maven Jar Plugin](https://maven.apache.org/plugins/maven-jar-plugin/)

- [7] [Maven Javadoc Plugin](https://maven.apache.org/plugins/maven-javadoc-plugin/)

- [8] [Maven Tomcat](http://tomcat.apache.org/maven-plugin.html)




