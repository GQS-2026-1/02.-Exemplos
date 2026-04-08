# 📦 Projeto Cadastro de Produtos com Cucumber em Java

Este projeto demonstra como utilizar **Java**, **JUnit 5** e **Cucumber** para automatizar **testes de aceitação (BDD)**, aplicando cenários baseados em comportamento para o **cadastro de vários produtos**.

---

## ✅ Pré-requisitos

Certifique-se de ter instalado:

| Ferramenta | Versão recomendada |
|-----------|--------------------|
| Java | **21** |
| Apache NetBeans | **18 ou superior** |
| Maven | **3.9+** |
| Cucumber for Java | Incluído via dependências Maven |

Verifique a versão do Java:

```bash
java -version
```

---

## 🗂️ Estrutura do Projeto

```css
cadastro-produtos-cucumber
├── pom.xml
└── src
    ├── main
    │   └── java
    │        └── exemplo
    │            └── model
    │                 └── Produto.java
    │            └── service
    │                 └── CadastroProdutoService.java
    └── test
        ├── java
        │   └── exemplo
        │      ├── runner
        │         └── RunnerTest.java
        │      └── steps
        │           └── CadastroProdutoSteps.java
        └── resources
            └── features
                └── cadastro_produtos.feature
```

---

## 🛠️ 1. Criar o projeto Maven

Crie um projeto Maven no NetBeans e defina:

- **GroupId**: `exemplo`
- **ArtifactId**: `cadastro-produtos-cucumber`

---

## ⚙️ 2. Configurar o `pom.xml`

Crie o arquivo `pom.xml` com o conteúdo abaixo:

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
         http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <groupId>exemplo</groupId>
    <artifactId>cadastro-produtos-cucumber</artifactId>
    <version>1.0-SNAPSHOT</version>

    <properties>
        <maven.compiler.source>21</maven.compiler.source>
        <maven.compiler.target>21</maven.compiler.target>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>

        <cucumber.version>7.20.1</cucumber.version>
        <junit.jupiter.version>5.11.3</junit.jupiter.version>
        <junit.platform.version>1.11.3</junit.platform.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>io.cucumber</groupId>
            <artifactId>cucumber-java</artifactId>
            <version>${cucumber.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>io.cucumber</groupId>
            <artifactId>cucumber-junit-platform-engine</artifactId>
            <version>${cucumber.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <version>${junit.jupiter.version}</version>
            <scope>test</scope>
        </dependency>

        <dependency>
            <groupId>org.junit.platform</groupId>
            <artifactId>junit-platform-suite</artifactId>
            <version>${junit.platform.version}</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-surefire-plugin</artifactId>
                <version>3.2.5</version>
            </plugin>
        </plugins>
    </build>

</project>
```

---

## 🧱 3. Criar a classe `Produto`

Arquivo: `src/main/java/exemplo/model/Produto.java`

```java
package exemplo.model;

public class Produto {
    private String nome;
    private double preco;
    private int quantidade;

    public Produto(String nome, double preco, int quantidade) {
        this.nome = nome;
        this.preco = preco;
        this.quantidade = quantidade;
    }

    public String getNome() {
        return nome;
    }

    public double getPreco() {
        return preco;
    }

    public int getQuantidade() {
        return quantidade;
    }
}
```

---

## 🧾 4. Criar a classe de serviço

Arquivo: `src/main/java/exemplo/service/CadastroProdutoService.java`

```java
package exemplo.service;

import exemplo.model.Produto;
import java.util.ArrayList;
import java.util.List;

private final List<Produto> produtosArmazenados = new ArrayList<>();
    private String observacao;

    public void cadastrarProdutos(List<Produto> produtos) {
        produtosArmazenados.addAll(produtos);
    }

    public List<Produto> getProdutosArmazenados() {
        return produtosArmazenados;
    }

    public void salvarObservacao(String observacao) {
        this.observacao = observacao;
    }

    public String getObservacao() {
        return observacao;
    }
}
```

---

## 🥒 5. Criar a Feature com Gherkin

Arquivo: `src/test/resources/features/cadastro_produtos.feature`

```gherkin
#language: pt

@cadastro
Funcionalidade: Cadastro de produtos

  # Cenário para cadastrar vários produtos de uma vez
  @massa_de_dados
  Cenário: Cadastrar produtos com tabela
    Dado que os seguintes produtos foram informados:
      | nome    | preco | quantidade |
      | Livro   | 50.00 | 3          |
      | Caderno | 25.00 | 10         |
    Quando o cadastro for realizado
    Então o sistema deve armazenar os produtos corretamente

  @mensagem
  Cenário: Registrar observação do produto
    Dado que o usuário informou a seguinte descrição:
      """
      Produto destinado ao estoque escolar.
      Deve ser armazenado em local seco.
      """
    Quando salvar a observação
    Então o sistema deve registrar a descrição com sucesso
```

---

## 🔗 6. Criar os Steps Definitions

Arquivo: `src/test/java/exemplo/steps/CadastroProdutoSteps.java`

```java
package exemplo.steps;

import exemplo.model.Produto;
import exemplo.service.CadastroProdutoService;

import io.cucumber.datatable.DataTable;
import io.cucumber.java.pt.Dado;
import io.cucumber.java.pt.Quando;
import io.cucumber.java.pt.Então;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

public class CadastroProdutoSteps {

    private List<Produto> produtosInformados;
    private CadastroProdutoService service;
    private String descricaoInformada;

    public CadastroProdutoSteps() {
        this.produtosInformados = new ArrayList<>();
        this.service = new CadastroProdutoService();
    }

    @Dado("que os seguintes produtos foram informados:")
    public void que_os_seguintes_produtos_foram_informados(DataTable dataTable) {
        List<Map<String, String>> linhas = dataTable.asMaps(String.class, String.class);

        for (Map<String, String> linha : linhas) {
            String nome = linha.get("nome");
            double preco = Double.parseDouble(linha.get("preco"));
            int quantidade = Integer.parseInt(linha.get("quantidade"));

            produtosInformados.add(new Produto(nome, preco, quantidade));
        }
    }

    @Dado("que o usuário informou a seguinte descrição:")
    public void que_o_usuario_informou_a_seguinte_descricao(String descricao) {
        this.descricaoInformada = descricao;
    }

    @Quando("o cadastro for realizado")
    public void o_cadastro_for_realizado() {
        service.cadastrarProdutos(produtosInformados);
    }

    @Quando("salvar a observação")
    public void salvar_a_observacao() {
        service.salvarObservacao(descricaoInformada);
    }

    @Entao("o sistema deve armazenar os produtos corretamente")
    public void o_sistema_deve_armazenar_os_produtos_corretamente() {
        List<Produto> armazenados = service.getProdutosArmazenados();

        assertEquals(2, armazenados.size());

        assertEquals("Livro", armazenados.get(0).getNome());
        assertEquals(50.00, armazenados.get(0).getPreco());
        assertEquals(3, armazenados.get(0).getQuantidade());

        assertEquals("Caderno", armazenados.get(1).getNome());
        assertEquals(25.00, armazenados.get(1).getPreco());
        assertEquals(10, armazenados.get(1).getQuantidade());
    }

    @Entao("o sistema deve registrar a descrição com sucesso")
    public void o_sistema_deve_registrar_a_descricao_com_sucesso() {
        assertNotNull(service.getObservacao());
        assertEquals(descricaoInformada, service.getObservacao());
    }
}
```

### 🔍 O que esse step faz?

- **Dado**: lê a tabela e cria os objetos `Produto`
- **Quando**: realiza o cadastro
- **Então**: valida se todos os produtos foram armazenados corretamente

---

## ▶️ 7. Criar o Runner dos testes

Arquivo: `src/test/java/exemplo/runner/RunnerTest.java`

```java
package br.com.exemplo.runner;

import org.junit.platform.suite.api.ConfigurationParameter;
import org.junit.platform.suite.api.IncludeEngines;
import org.junit.platform.suite.api.SelectClasspathResource;
import org.junit.platform.suite.api.Suite;

import static io.cucumber.junit.platform.engine.Constants.GLUE_PROPERTY_NAME;
import static io.cucumber.junit.platform.engine.Constants.PLUGIN_PROPERTY_NAME;

@Suite
@IncludeEngines("cucumber")
@SelectClasspathResource("features")
@ConfigurationParameter(key = GLUE_PROPERTY_NAME, value = "exemplo.steps")
@ConfigurationParameter(key = PLUGIN_PROPERTY_NAME, value = "pretty")
public class RunnerTest {
}
```

📌 O Runner é responsável por localizar e executar os cenários da pasta `features`.

---

## 🚀 8. Como executar os testes

No terminal, dentro da pasta do projeto:

```bash
mvn test
```

Ou via NetBeans:

- Clique com o botão direito no projeto
- Selecione **Test**
