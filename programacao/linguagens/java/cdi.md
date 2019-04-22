# JUnit 5

## Mudanças
O JUnit 5 (antigamente chamado de JUnit lambda) é uma revisão do projeto, adicionando novas funcionalidades suportadas pelo Java8. O projeto foi separado em diferentes módulos e garante compatibilidade com as versões anteriores.

Os novos módulos do JUnit são:

- JUnit Platform: é a fundação do framework de teste que contém as funcionalidades para execução de testes e pontos de extensão para construção de plugins maven, gradle e etc;
- JUnit Jupiter: a nova plataforma do JUnit;
- JUnit Vintage: provê um TestEngine para execução do JUnit 3 e 4;


## Configuração
Para adicionar o JUnit 5 utilizando o Maven, devemos adicionar como dependência a nova plataforma:

    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-api</artifactId>
        <version>5.0.0</version>
        <scope>test</scope>
    </dependency>

E adicionalmente, devemos configurar o plugin do surefire.

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.19</version>
                <dependencies>
                    <dependency>
                        <groupId>org.junit.platform</groupId>
                        <artifactId>junit-platform-surefire-provider</artifactId>
                        <version>1.0.0</version>
                    </dependency>
                    <dependency>
                        <groupId>org.junit.jupiter</groupId>
                        <artifactId>junit-jupiter-engine</artifactId>
                        <version>5.0.0</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>

### Executando testes do JUnit 4 e Junit 5
Para executar testes do JUnit 4 junto aos do JUnit 5 é necessário adicionar a dependência ao módulo `junit-vintage-engine` e adicionar a dependência do JUnit 4 ao projeto.

    <build>
        <plugins>
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.19</version>
                <dependencies>
                    <dependency>
                        <groupId>org.junit.platform</groupId>
                        <artifactId>junit-platform-surefire-provider</artifactId>
                        <version>1.1.0</version>
                    </dependency>
                    <dependency>
                        <groupId>org.junit.jupiter</groupId>
                        <artifactId>junit-jupiter-engine</artifactId>
                        <version>5.0.0</version>
                    </dependency>
                    <dependency>
                        <groupId>org.junit.vintage</groupId>
                        <artifactId>junit-vintage-engine</artifactId>
                        <version>4.12.0</version>
                    </dependency>
                </dependencies>
            </plugin>
        </plugins>
    </build>

### Filtrando por Tags
Podemos filtrar os testes de acordo com suas tags (veja [Tags e Filtros](#tags-e-filtros))

    <build>
        <plugins>
            ...
            <plugin>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>2.19</version>
                <configuration>
                    <properties>
                        <includeTags>acceptance</includeTags>
                        <excludeTags>integration,regression</excludeTags>
                    </properties>
                </configuration>
                <dependencies>
                    ...
                </dependencies>
            </plugin>
        </plugins>
    </build>

### Junit Launcher
É uma ferramenta de linha de comando [disponível no maven](https://repo1.maven.org/maven2/org/junit/platform/junit-platform-console-standalone/) que provê pretty printing para a execução de testes.

    ├─ JUnit Vintage
    │  └─ example.JUnit4Tests
    │     └─ standardJUnit4Test ✔
    └─ JUnit Jupiter
    ├─ StandardTests
    │  ├─ succeedingTest() ✔
    │  └─ skippedTest() ↷ for demonstration purposes
    └─ A special test case
        ├─ Custom test name containing spaces ✔
        ├─ ╯°□°）╯ ✔
        └─ 😱 ✔

    Test run finished after 64 ms
    [         5 containers found      ]
    [         0 containers skipped    ]
    [         5 containers started    ]
    [         0 containers aborted    ]
    [         5 containers successful ]
    [         0 containers failed     ]
    [         6 tests found           ]
    [         1 tests skipped         ]
    [         5 tests started         ]
    [         0 tests aborted         ]
    [         5 tests successful      ]
    [         0 tests failed          ]

Mais sobre o [Runner](http://junit.org/junit5/docs/current/user-guide/#running-tests-console-launcher)

### Mais sobre Execução de Testes
Mais sobre configuraão seção específica da [documentaçao](http://junit.org/junit5/docs/current/user-guide/#running-tests).

## Testes
### Anotações
As principais anotações do JUnit estão em `org.junit.jupiter.api`. algumas das anotações antigas tiveram seu comportamento alterado como é o caso da `@Test`.

Anotação | Descrição
-----------|------------
@Test | Denotes that a method is a test method. Unlike JUnit 4’s @Test annotation, this annotation does not declare any attributes, since test extensions in JUnit Jupiter operate based on their own dedicated annotations. Such methods are inherited unless they are overridden.
@ParameterizedTest | Denotes that a method is a parameterized test. Such methods are inherited unless they are overridden.
@RepeatedTest | Denotes that a method is a test template for a repeated test. Such methods are inherited unless they are overridden.
@TestFactory | Denotes that a method is a test factory for dynamic tests. Such methods are inherited unless they are overridden.
@TestInstance | Used to configure the test instance lifecycle for the annotated test class. Such annotations are inherited.
@TestTemplate | Denotes that a method is a template for test cases designed to be invoked multiple times depending on the number of invocation contexts returned by the registered providers. Such methods are inherited unless they are overridden.
@DisplayName | Declares a custom display name for the test class or test method. Such annotations are not inherited.
@BeforeEach | Denotes that the annotated method should be executed before each @Test, @RepeatedTest, @ParameterizedTest, or @TestFactory method in the current class; analogous to JUnit 4’s @Before. Such methods are inherited unless they are overridden.
@AfterEach | Denotes that the annotated method should be executed after each @Test, @RepeatedTest, @ParameterizedTest, or @TestFactory method in the current class; analogous to JUnit 4’s @After. Such methods are inherited unless they are overridden.
@BeforeAll | Denotes that the annotated method should be executed before all @Test, @RepeatedTest, @ParameterizedTest, and @TestFactory methods in the current class; analogous to JUnit 4’s @BeforeClass. Such methods are inherited (unless they are hidden or overridden) and must be static (unless the "per-class" test instance lifecycle is used).
@AfterAll | Denotes that the annotated method should be executed after all @Test, @RepeatedTest, @ParameterizedTest, and @TestFactory methods in the current class; analogous to JUnit 4’s @AfterClass. Such methods are inherited (unless they are hidden or overridden) and must be static (unless the "per-class" test instance lifecycle is used).
@Nested | Denotes that the annotated class is a nested, non-static test class. @BeforeAll and @AfterAll methods cannot be used directly in a @Nested test class unless the "per-class" test instance lifecycle is used. Such annotations are not inherited.
@Tag | Used to declare tags for filtering tests, either at the class or method level; analogous to test groups in TestNG or Categories in JUnit 4. Such annotations are inherited at the class level but not at the method level.
@Disabled | Used to disable a test class or test method; analogous to JUnit 4’s @Ignore. Such annotations are not inherited.
@ExtendWith | Used to register custom extensions. Such annotations are inherited.

#### Meta-anotações e anotações compostas
Diferente das antigas versões, cada teste poderá ser anotado com diversas anotações para definir seu comportamento. Em alguns casos, existem anotações que se repetem diversas vezes pelas Suítes de Teste.

Para evitar repetição de código o JUnit 5 permite que uma anotação composta seja definida, onde configuraremos as anotações que desejamos adicionar aos nossos testes (portanto, comportam-se como meta-anotações). Ao anotar um teste com esta nova anotação composta, este irá herdar o comportamento de todas as anotações associadas simultâneamente.


    import java.lang.annotation.ElementType;
    import java.lang.annotation.Retention;
    import java.lang.annotation.RetentionPolicy;
    import java.lang.annotation.Target;

    import org.junit.jupiter.api.Tag;

    @Target({ ElementType.TYPE, ElementType.METHOD })
    @Retention(RetentionPolicy.RUNTIME)
    @Tag("fast")
    public @interface Fast { }

### Classe de Teste Exemplo
A estrutura padrão de um Teste com o JUnit 5 é:

    class StandardTests {

        @BeforeAll
        static void initAll() {
        }

        @BeforeEach
        void init() {
        }

        @Test
        void succeedingTest() {
        }

        @Test
        void failingTest() {
            fail("a failing test");
        }

        @Test
        @Disabled("for demonstration purposes")
        void skippedTest() {
            // not executed
        }

        @AfterEach
        void tearDown() {
        }

        @AfterAll
        static void tearDownAll() {
        }

    }

### Nomes de Exibição
É possível definir nomes de exibição para os testes utilizando a anotaçao `@DisplayName`. Finalmente, BDD de graça!

    @DisplayName("A special test case")
    class DisplayNameDemo {

        @Test
        @DisplayName("Custom test name containing spaces")
        void testWithDisplayNameContainingSpaces() {
        }

        // More tests

    }

### Assertions
As principais mudanças das asserções são 1) as mensagens agora são o último parâmetro, finalmente e 2) uso intensivo de lambdas.

#### Mensagens
No teste abaixo, note que é possível criar uma mensagem que será avaliada de maneira lazy, apenase se o teste falhar.

    class AssertionsDemo {

        @Test
        void standardAssertions() {
            assertEquals(2, 2);
            assertEquals(4, 4, "The optional assertion message is   now the last parameter.");
            assertTrue(2 == 2, () -> "Assertion messages can be     lazily evaluated -- "
                    + "to avoid constructing complex messages   unnecessarily.");
        }

        // More Tests

    }

#### AssertAll
Há uma nova asserção chamada `assertAll` que recebe uma lista de asserções. Todas as asserções são executadas e, se existirem, todas as falhas serão reportadas.

    @Test
    void groupedAssertions() {
        // In a grouped assertion all assertions are executed, and any
        // failures will be reported together.
        assertAll("person",
            () -> assertEquals("John", person.getFirstName()),
            () -> assertEquals("Doe", person.getLastName())
        );
    }

É possível criar estruturas complexas utilizando o `assertAll`.

    @Test
    void dependentAssertions() {
        // Within a code block, if an assertion fails the
        // subsequent code in the same block will be skipped.
        assertAll("properties",
            () -> {
                String firstName = person.getFirstName();
                assertNotNull(firstName);

                // Executed only if the previous assertion is valid.
                assertAll("first name",
                    () -> assertTrue(firstName.startsWith("J")),
                    () -> assertTrue(firstName.endsWith("n"))
                );
            },
            () -> {
                // Grouped assertion, so processed independently
                // of results of first name assertions.
                String lastName = person.getLastName();
                assertNotNull(lastName);

                // Executed only if the previous assertion is valid.
                assertAll("last name",
                    () -> assertTrue(lastName.startsWith("D")),
                    () -> assertTrue(lastName.endsWith("e"))
                );
            }
        );
    }

#### Exceptions
Para avaliarmos se uma `Exception` é lançada, agora utilizamos o assert `assertThrows`. A vantagem deste novo método é que obtemos a instância da exceção, o que permite que avaliemos mensagens e outros dados.

    @Test
    void exceptionTesting() {
        Throwable exception = assertThrows(IllegalArgumentException.class, () -> {
            throw new IllegalArgumentException("a message");
        });
        assertEquals("a message", exception.getMessage());
    }


#### Timeout
Existem um novo assert para avaliar o tempo de resposta de um procedimento, `assertTimeout`.

    @Test
    void timeoutExceeded() {
        // The following assertion fails with an error message similar to:
        // execution exceeded timeout of 10 ms by 91 ms
        assertTimeout(ofMillis(10), () -> {
            // Simulate task that takes more than 10 ms.
            Thread.sleep(100);
        });
    }

### Assumptions
Assumptions é um recurso antigo do JUnit 4 que também foi melhorado nesta nova versão. Uma Assumption é uma teste utilizado para garantir que o ambiente do teste é adequado e, caso contrário, o teste será ignorado. Este é um recurso útil para verificar configurações externas ou outros detalhes que possam causar a quebra do teste mas que não possuam relação com o está sendo testado em si (uma conexão com o BD, por exemplo).

    class AssumptionsDemo {

        @Test
        void testOnlyOnCiServer() {
            assumeTrue("CI".equals(System.getenv("ENV")));
            // remainder of test
        }

        // More tests

    }

Todas as Assumptions são métodos estáticos. Mais em [documentação de usuario](http://junit.org/junit5/docs/current/user-guide/#writing-tests-assumptions) e todos os métodos listados no [javadoc](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/Assumptions.html)

### Desabilitando testes
Desligamos testes com a anotação `@Disabled`, que pode ser aplicada em classes ou métodos.

    @Disabled
    class DisabledClassDemo {
        @Test
        void testWillBeSkipped() {
        }
    }


### Tags e Filtros
Podemos utilizar a anotação `Tag()` para aplicar tags em um teste. Estas tags são úteis para que testes sejam filtrados posteriormente.

    @Tag("fast")
    @Tag("model")
    class TaggingDemo {

        @Test
        @Tag("taxes")
        void testingTaxCalculation() {
        }

    }

Os nomes das Tags devem obedecer às seguintes regras:
- Nâo podem ser `null` ou um texto vazio;
- Não deve conter espaço em branco
- Não de conter caracteres de controle ISO
- Não deve conter os caracteres reservados `,`, `(`, `)`, `&`, `|`, `!`;

### Ciclo de vida da instância do Teste
Como nas versões anteriores, o comportamento default do JUnit é que uma nova instância da classe de testes seja criada à cada teste, garantindo que estes sejam executados em isolamento.

No entanto, para casos em que todos os testes devam ser executados na mesma instância, podemos anota a classe de teste com a nova anotação `@TestInstance(Lifecycle.PER_CLASS)`.

Existem algumas opções com relação à esta configuração, veja [na documentação](http://junit.org/junit5/docs/current/user-guide/#writing-tests-test-instance-lifecycle-changing-default)

### Teste aninhados
É possível aninhar classes de teste, expressando melhor o relacionamento entre elas. Este aninhamento de classes pode ter uma profundidade arbitrária e, junto com os nomes indicados na anotação `@DisplayName` pode criar uma árvore de asserções com grande semântica.

    public class ListaTest {

        private List<String> lista;

        @Test
        @DisplayName("deve criar nova lista com new ArrayList()")
        void deveCriarNovaLista() {
            lista = new ArrayList<>();
        }

        @Nested
        @DisplayName("quando nova lista")
        class quandoNovaLista {

            @BeforeEach
            void setUp() {
                lista = new ArrayList<>();
            }

            @Test
            @DisplayName("deve estar vazia")
            void deveEstarVazia() {
                assertTrue(lista.isEmpty(), () -> "A lista não está     vazia! => " + lista);
            }

            @Test
            @DisplayName("deve adicionar novos valores com add")
            void deveAdicionarValoresComAdd() {
                lista.add("valor");
                assertFalse(lista.isEmpty(), () -> "A lista está    vazia após inserir um valor => " + lista);
            }

            @Nested
            @DisplayName("após adicionar um valor")
            class quandoPossuiValores {

                @BeforeEach
                void setUp() {
                    lista.add("valor1");
                    lista.add("valor2");
                    lista.add("valor3");
                }

                @DisplayName("deve retornar o número de elementos")
                @Test
                void deveRetornarONúmeroDeElementos() {
                    assertEquals(3, lista.size());
                }
            }

        }
    }

### Injeção de dependências para Construtores e Métodos
O Junit 5 permite que construtores e metodos possuem argumentos , permitindo uma maior flexibilidade e permite que injeção de dependências seja habilitada para construtores e métodos.

Qualquer método anotado com `@Test`, `@TestFactory`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, ou `@AfterAll` é candidato à injeção de dependências.

O [ParameterResolver](http://junit.org/junit5/docs/current/api/org/junit/jupiter/api/extension/ParameterResolver.html) do JUnit define a API para que extensões de teste sejam desenvolvidas (frameworks). Estes `ParameterResolver` customizados, uma vez registrados permitem que diferentes instâncias sejam injetadas. Um exemplo, é a versão nova do Mockito:

    @ExtendWith(MockitoExtension.class)
    class MyMockitoTest {

        @BeforeEach
        void init(@Mock Person person) {
            when(person.getName()).thenReturn("Dilbert");
        }

        @Test
        void simpleTestWithInjectedMock(@Mock Person person) {
            assertEquals("Dilbert", person.getName());
        }

    }

Por padrão, o JUnit já disponibiliza os seguintes objetos:

Tipo | Descrição
-----|----------
TestInfo | Provê informações reflexivas sobre o próprio teste como, por exemplo, seu nome de display e outros dados.
RepetitionInfo | Para os métodos que repetem-se ( os anotados com `@RepeatedTest`, `@BeforeEach`, ou `@AfterEach`) é possível injetar o tipo `RepetitionInfo` que possui os dados da repetição atual e quantidade de repetições.
TestReporter | Objeto que provê a possibilidade de inserir mais informações sobre o teste. Qualquer lugar onde imprimiríamos dados para o `stdout` ou `stderr` devemos utilizar objeto.

Demonstração de uso do `TestInfo`:

    @Test
    @DisplayName("TEST 1")
    @Tag("my-tag")
    void test1(TestInfo testInfo) {
        assertEquals("TEST 1", testInfo.getDisplayName());
        assertTrue(testInfo.getTags().contains("my-tag"));
    }

Podemos utilizar o `TestReporter` da seguinte forma:

    class TestReporterDemo {

        @Test
        void reportSingleValue(TestReporter testReporter) {
            testReporter.publishEntry("a key", "a value");
        }

        @Test
        void reportSeveralValues(TestReporter testReporter) {
            HashMap<String, String> values = new HashMap<>();
            values.put("user name", "dk38");
            values.put("award year", "1974");

            testReporter.publishEntry(values);
        }

    }

Na execução, será impresso "timestamp = 2017-09-16T12:53:21.366, user name = dk38, award year = 1974'. Também é possível chamar o método `publicEntry` adicionando uma chave e valor, da seguinte forma:

    testReporter.publishEntry(chave, valor);

O detalhe é que, internamente, usa-se também um map e, quando adicionados desta forma, os valores serão exibidos separadamente:

    timestamp = 2017-09-16T12:57:11.752, 1 = valor1
    timestamp = 2017-09-16T12:57:11.755, 2 = valor2
    timestamp = 2017-09-16T12:57:11.755, 3 = valor3

### Test Interfaces e Métodos Default
É possível declarar` @Test`, @`RepeatedTest`, `@ParameterizedTest`, `@TestFactory`, `@TestTemplate`, `@BeforeEach`, e `@AfterEach` em métodos estáticos ou default em interfaces. As anotações `@BeforeAll` e `@AfterAll` também podem ser usadas como estáticas desde que a classe esteja anotada com `@TestInstance(Lifecycle.PER_CLASS)` (veja [Ciclo de vida da instância do Teste](#ciclo-de-vida-da-instância-do-teste))

Esta funcionalidade é útil para que possamos aplicar testes padrão e construir contratos com os quais determinados objetos deverão estar em conformidade, estabelecer padrões de mensagens de teste, verificação de tempo de execução e etc.

    @TestInstance(Lifecycle.PER_CLASS)
    interface TestLifecycleLogger {

        static final Logger LOG = Logger.getLogger  (TestLifecycleLogger.class.getName());

        @BeforeAll
        default void beforeAllTests() {
            LOG.info("Before all tests");
        }

        @AfterAll
        default void afterAllTests() {
            LOG.info("After all tests");
        }

        @BeforeEach
        default void beforeEachTest(TestInfo testInfo) {
            LOG.info(() -> String.format("About to execute [%s]",
                testInfo.getDisplayName()));
        }

        @AfterEach
        default void afterEachTest(TestInfo testInfo) {
            LOG.info(() -> String.format("Finished executing [%s]",
                testInfo.getDisplayName()));
        }

    }

Podemos também declarar extensões e tags em interfaces e estes serão herdados pelas classes que as implementarem.

    @Tag("timed")
    @ExtendWith(TimingExtension.class)
    interface TimeExecutionLogger { }

Em nossa classe:

    class TestInterfaceDemo implements TestLifecycleLogger,
            TimeExecutionLogger, TestInterfaceDynamicTestsDemo {

        @Test
        void isEqualValue() {
            assertEquals(1, 1, "is always equal");
        }

    }

Esta classe irá herdar todos os detalhes da interface. Isto é

### Testes com Repetição
É possível repetir um teste diversas vezes através da anotação `@RepeatedTest`, passando a quantidade de vezes que o teste deve ser repetir. O teste abaixo, irá se repetir 10 vezes.

    @RepeatedTest(10)
    void repeatedTest() {
        // ...
    }

Podemos definir um nome de exibição para as repetições através do atributo `name` da anotação `@RepeatedTest`. Existem alguns  placeholders que podemos utilizar para ajudar na identificação:

- {displayName}: nome do teste
- {currentRepetition}: repetição atual
- {totalRepetitions}: total de repetições.

O nome default para este tipo de testes serã "repetition {currentRepetition} of {totalRepetitions}". Para ter acesso à estes dados programaticamente, é possível injetar uma instância de `RepetitionInfo`.

    @DisplayName("deve suportar 5 valores")
    @RepeatedTest(value = 5, name = "repetição {currentRepetition}/ {totalRepetitions}")
    void deveSuportar5Valores(RepetitionInfo repetitionInfo) {
        lista.add("valor " + repetitionInfo.getCurrentRepetition())
    }

Existem muitos outros exemplos na [documentação oficial](http://junit.org/junit5/docs/current/user-guide/#writing-tests-repeated-tests-examples).

### Testes Parametrizáveis
Testes parametrizáveis são testes que podem receber valores como argumentos. Estes testes são anotados com `@ParameterizedTest` e uma anotação indicando a fonte de dados como, por exemplo, `@ValueSource` onde, esta segunda anotação, indica os valores que serão passados como argumento para o teste.

    @ParameterizedTest
    @ValueSource(strings = { "Hello", "World" })
    void testWithStringParameter(String argument) {
        assertNotNull(argument);
    }

Para utilizar estes testes, é necessário adicionar o módulo `junit-jupiter-params` como dependencia.

    <dependency>
        <groupId>org.junit.jupiter</groupId>
        <artifactId>junit-jupiter-params</artifactId>
        <version>5.0.0</version>
        <scope>test</scope>
    </dependency>

Existem diversos sources de valores:

- @ValueSource
- @EnumSource
- @MethodSource
- @CsvSource
- @CsvFileSource
- @ArgumentsSource

Um dos mais utilizados,`@MethodSource`, recebe o nome de um método presente na classe de teste. Este método deve ser estático a não ser que a classe esteja anotada com `@TestInstance(Lifecycle.PER_CLASS)`. O retorno deste método deve ser um `Stream`, `Iterable`, `Iterator`, ou array.

    @ParameterizedTest
    @MethodSource("stringProvider")
    void testWithSimpleMethodSource(String argument) {
        assertNotNull(argument);
    }

    static Stream<String> stringProvider() {
        return Stream.of("foo", "bar");
    }

Sobre os demais, consulte a [documentação](http://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-sources)

É possível definir o nome de exibição dos testes parametrízáveis:

    @DisplayName("Display name of container")
    @ParameterizedTest(name = "{index} ==> first=''{0}'', second={1}    ")
    @CsvSource({ "foo, 1", "bar, 2", "'baz, qux', 3" })
    void testWithCustomDisplayNames(String first, int second) { }

Os valores `{0}` e `{1}` são o índice do argumento recebido pelo método.

    Display name of container ✔
    ├─ 1 ==> first='foo', second=1 ✔
    ├─ 2 ==> first='bar', second=2 ✔
    └─ 3 ==> first='baz, qux', second=3 ✔

Veja mais na [documentação](http://junit.org/junit5/docs/current/user-guide/#writing-tests-parameterized-tests-display-names).

### Test Templates
A nova versão do JUnit inclui um tipo inteiramente novo de testes: testes dinâmicos. Estes testes são gerados totalmente em tempo de execução e possuem estrutura diferente da usual. Estes testes são criados por TestFactories.

Os testes dinâmicos devem retornar um `Stream`, `Collection` , `Iterable`, `Iterator` de `DynamicNode`. O tipo `DynamicNode` possui duas sublcasses instanciáveis: `DynamicContainer` e `DynamicTest`.

Um `DynamicContainer` é composto de um Display Name e uma lista de nós filho, possibilitando a criação de hierarquias de nós dinâmicos.

Um `DynamicTest` é um caso de testes gerado em tempo de execução e é composto de display name e um `Executable`. Um `Executable` é uma interface funcional, o que significa que podemos construir testes dinâmicos com lambdas.

*Nota*: este é um recurso experimental.

    class DynamicTestsDemo {

        // Vai resultar em um erro pois o tipo de retorno é inválido!
        @TestFactory
        List<String> dynamicTestsWithInvalidReturnType() {
            return Arrays.asList("Hello");
        }

        @TestFactory
        Collection<DynamicTest> dynamicTestsFromCollection() {
            return Arrays.asList(
                dynamicTest("1st dynamic test", () -> assertTrue    (true)),
                dynamicTest("2nd dynamic test", () -> assertEquals  (4, 2 * 2))
            );
        }

        @TestFactory
        Iterable<DynamicTest> dynamicTestsFromIterable() {
            return Arrays.asList(
                dynamicTest("3rd dynamic test", () -> assertTrue    (true)),
                dynamicTest("4th dynamic test", () -> assertEquals  (4, 2 * 2))
            );
        }

        // More tests

    }

Mais exemplos na [documentação](http://junit.org/junit5/docs/current/user-guide/#writing-tests-dynamic-tests-examples)
