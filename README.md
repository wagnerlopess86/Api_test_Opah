# 🤖 api-tests-Opah

> Projeto responsável pelos testes automatizados de serviços

**Pré-Requisitos:**

- IDE recomendada: [IntelliJ IDEA Community Edition](https://www.jetbrains.com/pt-br/idea/download/?section=windows)
- Java [17.0.13]() instalado e [configurado](https://medium.com/@gauravshah97/how-to-install-java-on-windows-20fb8c329b74) na máquina;
- Maven [3.9.11](https://maven.apache.org/download.cgi) instalado e [configurado](https://medium.com/@gauravshah97/how-to-install-maven-on-windows-39ff317e40cf) na máquina;

⚠️ **OBS:** Após as instalações rode um `mvn clean install` na raiz do projeto.  
Isso fará com que sejam baixadas todas as dependências necessárias para rodar os testes.

---

### 🦺 Principais tecnologias utilizadas

* [Java](https://www.java.com/pt-BR/) - Linguagem utilizada para implementar os testes;
* [Junit5](https://junit.org/) - Framework de execução e asserção dos testes;
* [Maven](https://maven.apache.org/) - Gerenciador de dependências dos pacotes utilizados no projeto;
* [Rest-assured](https://rest-assured.io/) - Framework responsável pelas chamadas http e asserção dos testes;
* [Lombok](https://projectlombok.org/) - Biblioteca utilizada para reduzir o boilerplate code em Java;
* [Allure Reports](https://docs.qameta.io/allure/) - Ferramenta utilizada para gerar relatórios de testes;
* [Checkstyle](https://checkstyle.sourceforge.io/) - Ferramenta utilizada para manter o padrão de código do projeto;
* [SLF4J](http://www.slf4j.org/) - Biblioteca utilizada para realizar logs durante a execução dos testes;

⚠️ **OBS:** Para verificar as versões das tecnologias utilizadas e demais dependências, acesse o arquivo [pom.xml](pom.xml).

---

### 📐 Padrões do projeto

---

### 📂 Organização de pastas:

> Para uma melhor organização dos testes, optamos por criar um pacote para cada serviço que está sendo testado.<br>
> Seguimos assim o conceito de que cada serviço pode e deve ser testado de forma independente, mas também permitindo integrações.
> 
> Dentro de cada pacote de serviço teremos subpacotes que visam dividir os arquivos por responsabilidade.<br>
> Evitando gerar classes enormes com muitos métodos, responsabilidades cruzadas ou incoerentes com seu objetivo.
> 
> Desta forma, entendemos que conseguimos otimizar a organização, manutenção e execução dos testes.

<br>
Exemplo da estrutura de pastas:
<br><br>

🗂️ [ services ]  
└─> 🗂️ [ serviceName ]  
&ensp;&ensp;&ensp;&ensp;├─> 📁 [ contracts ]    
&ensp;&ensp;&ensp;&ensp;│&ensp;&ensp;&ensp;└─> 📄 MethodPathContract.json  
&ensp;&ensp;&ensp;&ensp;├─> 📁 [ domains ]  
&ensp;&ensp;&ensp;&ensp;│&ensp;&ensp;&ensp;└─> 📄 ObjectNameDomain.java     
&ensp;&ensp;&ensp;&ensp;├─> 📁 [ requests ]  
&ensp;&ensp;&ensp;&ensp;│&ensp;&ensp;&ensp;└─> 📄 MethodPathRequest.java   
&ensp;&ensp;&ensp;&ensp;└─> 📁 [ tests ]  
&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;&ensp;└─> 📄 MethodPathTest.java

---

### 💻 Implementando testes:

Toda classe de **teste** deve extender a classe `BaseTest`.  
Toda classe de **request** deve extender a classe `BaseRequest`.

Para cada **classe de teste** criada, deve-se adicionar as seguintes `annotations`:
- `@Feature` para descrever o objetivo do serviço que está a ser testado;
- `@Link` para informar a _url_ da documentação (Swagger) do serviço (atentar-se para a indentação);
- `@TestMethodOrder(MethodOrderer.DisplayName.class)` para executar os testes por ordem alfabética, facilitando a leitura e execução dos testes;
- `@Slf4j` (opcional) para adicionar o logger na classe de teste e facilitar a depuração dos testes (o conteúdo logado vai para o report junto com o resultado);

**Exemplo:**

```java
@Feature("Consulta de usuários por id")
@Link(
    name = SWAGGER_SERVE_REST,
    url = "https://serverest.dev/#/Usu%C3%A1rios/get_usuarios___id_"
)
@Slf4j
@TestMethodOrder(MethodOrderer.DisplayName.class)
public class GetUsersByIdTest extends BaseTest {}
````

Para cada **método de teste** implementado, deve-se adicionar as seguintes `annotations`:

- `@Test` para identificar o método de teste;
- `@WithoutLogs` **quando necessário**, para não logar a request e a response quando der erro no teste;
- `@SneakyThrows` **quando necessário**, para não precisar declarar todas as exceptions no método de teste;
- `@DisplayName("Deve ...")` para identificar melhor o cenário implementado no teste, **deve ser IGUAL ao cenário de teste**;
- `@Link` para adicionar a _url_ do cenário de teste criado no Testmo, **isso NÃO CRIA um VÍNCULO de atualização apenas aponta para o teste no repositório**;
- `@Tags({@Tag("service-name"), @Tag("test-type")})` para identificar o serviço (api) e o tipo de teste na execução, ver documentação sobre os [tipos de tags utilizados](README.md#-tags-de-testes);

**Exemplo:**

```java
@Test
@DisplayName("Deve retornar campos de acordo com o contrato")
@Link(name = CENARIO_DE_TESTE, url = "https://")
@Tags({@Tag("serverest"), @Tag("contract")})
public void shouldReturnFieldsAccordingToTheContract() {}
```

⚠️ **OBS:** Caso seja preciso ignorar a execução de um teste devido a este estar sob **implementação**, **manutenção** ou
**investigação**, utilizar `@Disabled("Comment")`, **justificando o motivo do teste estar desativado**.

**Exemplo:**
````java
@Test
@Disabled("""
    🚧 Cenário de teste falhando devido a um bug encontrado
    🐞 [BUG] [serverest] Filtros page e size não estão sendo aplicados na consulta de transações da fatura
    🔗 https://
    """)
@DisplayName("Deve retornar campos de acordo com o contrato")
@Link(name = CENARIO_DE_TESTE, url = "https://")
@Tags({@Tag("serverest"), @Tag("contract")})
public void shouldReturnFieldsAccordingToTheContract() {}
````
---

### 🚩 Tags de Testes

As tags de testes são utilizadas para organizar os testes, permitindo que sejam executados de forma mais eficiente e direcionada.  
Elas são definidas na annotation `@Tags` e podem ser utilizadas para identificar o serviço (api) e o tipo de teste.

#### Exemplos de tags:
- `@Tag("serverest")` - Utilizada para identificar o serviço relacionado aos testes;
- `@Tag("validation")` - Utilizada para identificar testes de validação (ex: validação de payload);
- `@Tag("integration")` - Utilizada para identificar testes que possuem integração com outro serviço/banco para validar o teste;
- `@Tag("pagination")` - Utilizada para identificar testes de paginação;
- `@Tag("contract")` - Utilizada para identificar testes de contrato;
- `@Tag("one-shot")` - Utilizada para identificar testes de execução única (ex: testes em que a massa não é reutilizável);

---

### 🏁 Execução dos Testes

---


### 🔨 Executar testes via IntelliJ:

- Para executar todos os testes de uma classe, clique com o botão direito do mouse na classe e selecione a opção "Run 'NomeDaClasse'";
- Para executar todos os testes de um pacote, clique com o botão direito do mouse no pacote e selecione a opção "Run 'NomeDoPacote'";
- Para executar todos os testes do projeto, clique com o botão direito do mouse na pasta `tests` e selecione a opção "Run 'tests'";
- Para executar um teste específico, clique com o botão direito do mouse no método de teste e selecione a opção "Run 'NomeDoTeste'";

⚠️ **OBS:** Configurar execução para `profile=local` no IntelliJ:
  ```
  - Acessar o menu "Run";
  - Clicar em "Edit Configurations";
  - Abrir opção "Junit' -> Runner";
  - No campo "Environment Variables" informar "profile=local";
  - Clicar em "Apply" e depois em "OK";
  - Após isso, o profile local será utilizado para rodar os testes;
  ```


### 🚀 Executar testes pelo Maven:

- Executar todos os testes que possuam uma determinada **TAG**:
    ```sh
    mvn test -Dtag="serverest"
    ```
  _Para rodar mais de uma tag de teste utilize **vírgula** ou **pipe** entre as tags:_
  ```sh
  mvn test -Dtag="serverest,outra-api"
  mvn test -Dtag="serverest | outra-api"
  ```
  _Para rodar testes que possuem mais de uma tag utilize **e comercial** entre as tags:_
  ```sh
  mvn test -Dtag="serverest & contract"
  ```

- Executar todos os testes de uma determinada **CLASSE**:
  ```sh
  mvn test -Dtest=GetUsersByIdTest
  ```
  _Para rodar mais de uma classe de teste basta inserir o nome das classes separadas por vírgula:_
  ```sh
  mvn test -Dtest=GetUsersByIdTest,GetUsersTest
  ```

- Executar todos os testes de um determinado **PACOTE**:
  ```sh
  mvn test -Dtest="services.serveRest.tests.**"
  ```

- Executar **MÉTODO** de teste de uma determinada classe:
  ```sh
  mvn test -Dtest=GetUsersByIdTest#shouldReturnFieldsAccordingToTheContract
  ```
  _Para rodar mais de um teste da mesma classe basta inserir o nome dos métodos separados por vírgula:_
  ```sh
  mvn test -Dtest=PostUsersTest#shouldSuccessfullyRegisterAUser,shouldReturnErrorWhenNotProvidingMandatoryFields
  ```

  ⚠️ **OBS:** É possível rodar os testes com **MENOS logs**, para isso basta informar `-Dcustom.log.enabled=false` na execução do maven:
  ```sh
  mvn test -Dtest=GetUsersTest -Dcustom.log.enabled=false
  ```

---

### 📄 Gerar relatório de testes pelo Allure Reports:

1. Executar os testes com comandos maven de teste conforme passado no tópico anterior:
   ```sh
    mvn test -Dtag="serverest"
    ```

2. Executar o comando maven do Allure Reports:
    ```sh
    mvn allure:serve
    ```
3. Será aberto uma página web no navegador com o relatório de testes.

---

### 🕵️‍ Configurando e Rodando o checkstyle:

- #### 🔨 Configurar checkstyle no IntelliJ:
  
  - Na sua IDE acesse o menu "File > Settings > Tools > Checkstyle";
  - Em "Scan Scope" selecione a opção "Only Java Resources (including tests)";
  - Na área de "Configuration File" clique em "+" (adicionar);
  - Informe uma "Description" (sugestão: "Realize Checks");
  - Marque a opção "Use a local Checkstyle file" e clique em "Browse";
  - Navegue até o local do arquivo [checkstyle.xml](checkstyle.xml) (raiz do projeto) e dê "OK";
  - Após selecionar o arquivo, clique em "Next", "Finish", "Apply" e "OK";<p>

- #### 🔍 Rodar checkstyle no IntelliJ:
  
  - Na sua IDE acesse o menu "View > Tool Windows > CheckStyle";
  - No menu superior, selecione no campo "Rules" o checkstyle que deseja rodar (será exibido o nome do checkstyle que você configurou);
  - No menu lateral esquerdo, clique no ícone ▶ (play) para rodar o checkstyle no arquivo que está aberto atualmente, OU no ícone 📂 (pasta) para rodar no projeto todo;
<p>

⚠️ **OBS:** Passos realizados com _IntelliJ IDEA 2024.2.4 (Community Edition)_.

---

### 🧭 Abrindo um PR para branch develop:

---

#### ✅ Para abertura de PRs orientamos seguir os seguintes etapas:

- [Formatar o código de acordo com o codestyle](README.md#-configurando-e-aplicando-o-codestyle);
- [Rodar os testes](README.md#-executar-testes-pelo-maven) e o [checkstyle](README.md#-configurando-e-rodando-o-checkstyle) ANTES de submeter o PR;
- Informar no PR o link da demanda (Card) que originou a alteração;
- Informar no PR o link dos cenários escritos para os testes implementados;
- Informar no PR o link da documentação (Swagger/Eventos) referente aos testes implementados;
- Informar no PR a evidência de execução (Print) dos testes implementados passando com sucesso;

#### 💡 Dicas para a abertura de PRs:

- Faça PRs pequenos e objetivos a fim de facilitar o code review;
- Somente suba PRs de testes que estão rodando e funcionando;
- Caso seja necessário, para uma mesma demanda, faça vários PRs;

️⚠️ **OBS**: Para o merge do PR é necessário que tenha ao menos **UM** revisor.

---

### 🌐 Links úteis:

https://regex101.com/ - Build, test, and debug regex  
https://jsonschema.net/ - Transform json response to json schema  
https://mvnrepository.com/ - Repositório de dependências Maven  
https://www.baeldung.com/junit-5-tags - Documentação sobre tags no Junit5
