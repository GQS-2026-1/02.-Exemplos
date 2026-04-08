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

- **GroupId**: `br.com.exemplo`
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

    <groupId>br.com.exemplo</groupId>
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

Arquivo: `src/main/java/br/com/exemplo/model/Produto.java`

```java
package br.com.exemplo.model;

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

Arquivo: `src/main/java/br/com/exemplo/service/CadastroProdutoService.java`

```java
package br.com.exemplo.service;

import br.com.exemplo.model.Produto;
import java.util.ArrayList;
import java.util.List;

public class CadastroProdutoService {

    private final List<Produto> produtosArmazenados = new ArrayList<>();

    public void cadastrarProdutos(List<Produto> produtos) {
        produtosArmazenados.addAll(produtos);
    }

    public List<Produto> getProdutosArmazenados() {
        return produtosArmazenados;
    }
}
```

---

## 🥒 5. Criar a Feature com Gherkin

Arquivo: `src/test/resources/features/cadastro_produtos.feature`

```gherkin
#language: pt

Funcionalidade: Cadastro de produtos

  Cenário: Cadastrar vários produtos
    Dado que os seguintes produtos foram informados:
      | nome     | preco | quantidade |
      | Caderno  | 20.00 | 10         |
      | Caneta   | 5.00  | 30         |
      | Mochila  | 80.00 | 5          |
    Quando o cadastro for realizado
    Então o sistema deve armazenar todos os produtos corretamente
```

---

## 🔗 6. Criar os Steps Definitions

Arquivo: `src/test/java/br/com/exemplo/steps/CadastroProdutoSteps.java`

```java
package br.com.exemplo.steps;

import br.com.exemplo.model.Produto;
import br.com.exemplo.service.CadastroProdutoService;
import io.cucumber.datatable.DataTable;
import io.cucumber.java.pt.Dado;
import io.cucumber.java.pt.Quando;
import io.cucumber.java.pt.Então;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import static org.junit.jupiter.api.Assertions.assertEquals;

public class CadastroProdutoSteps {

    private List<Produto> produtosInformados;
    private CadastroProdutoService service;

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

    @Quando("o cadastro for realizado")
    public void o_cadastro_for_realizado() {
        service.cadastrarProdutos(produtosInformados);
    }

    @Então("o sistema deve armazenar todos os produtos corretamente")
    public void o_sistema_deve_armazenar_todos_os_produtos_corretamente() {
        List<Produto> armazenados = service.getProdutosArmazenados();

        assertEquals(3, armazenados.size());

        assertEquals("Caderno", armazenados.get(0).getNome());
        assertEquals(20.00, armazenados.get(0).getPreco());
        assertEquals(10, armazenados.get(0).getQuantidade());

        assertEquals("Caneta", armazenados.get(1).getNome());
        assertEquals(5.00, armazenados.get(1).getPreco());
        assertEquals(30, armazenados.get(1).getQuantidade());

        assertEquals("Mochila", armazenados.get(2).getNome());
        assertEquals(80.00, armazenados.get(2).getPreco());
        assertEquals(5, armazenados.get(2).getQuantidade());
    }
}
```

### 🔍 O que esse step faz?

- **Dado**: lê a tabela e cria os objetos `Produto`
- **Quando**: realiza o cadastro
- **Então**: valida se todos os produtos foram armazenados corretamente

---

## ▶️ 7. Criar o Runner dos testes

Arquivo: `src/test/java/br/com/exemplo/runner/RunnerTest.java`

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
@ConfigurationParameter(key = GLUE_PROPERTY_NAME, value = "br.com.exemplo.steps")
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
