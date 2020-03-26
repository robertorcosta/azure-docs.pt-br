---
title: Tutorial – Testar módulos do Terraform no Azure usando o Terratest
description: Saiba como usar o Terratest para testar seus módulos do Terraform.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 687a793af2b9b75efe463b042d121c32f18974d6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370790"
---
# <a name="tutorial-test-terraform-modules-in-azure-using-terratest"></a>Tutorial: Testar módulos do Terraform no Azure usando o Terratest

> [!NOTE]
> O código de exemplo neste artigo não funciona com a versão 0.12 (e superior).

Você pode usar módulos do Azure Terraform para criar componentes que podem ser testados, reutilizados e combinados. Os módulos do Terraform incorporam encapsulamento, o que é útil para implementar a infraestrutura como processos de código.

É importante implementar garantia de qualidade ao criar módulos do Terraform. Infelizmente, há pouca documentação disponível explicando como criar testes de unidade e testes de integração em módulos do Terraform. Este tutorial apresenta uma infraestrutura de teste e melhores práticas que adotamos durante a compilação de nossos [módulos do Azure Terraform](https://registry.terraform.io/browse?provider=azurerm).

Nós examinamos a maioria das infraestruturas de teste populares e escolhemos o [Terratest](https://github.com/gruntwork-io/terratest) para usar nos testes de nossos módulos do Terraform. O Terratest é implementado como uma biblioteca Go. Ele fornece um conjunto de padrões e funções auxiliares para tarefas de teste de infraestrutura comuns, como fazer solicitações HTTP e usar SSH para acessar uma máquina virtual específica. A lista abaixo descreve algumas das principais vantagens de usar o Terratest:

- **Ele fornece auxiliares práticos para verificar a infraestrutura.** Esse recurso é útil quando você deseja verificar sua infraestrutura real no ambiente real.
- **A estrutura de pastas é organizada claramente**. Os casos de teste são organizados claramente e seguem a [estrutura de pastas do módulo do Terraform padrão](https://www.terraform.io/docs/modules/create.html#standard-module-structure).
- **Todos os casos de teste são escritos em linguagem Go.** A maioria dos desenvolvedores que usa o Terraform é de desenvolvedores Go. Se você for um desenvolvedor Go, não precisará aprender outra linguagem de programação para usar o Terratest. Além disso, as únicas dependências necessárias para executar casos de teste no Terratest são Go e Terraform.
- **A infraestrutura é altamente extensível**. Você pode estender funções adicionais sobre o Terratest, incluindo recursos específicos do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo prático não depende de plataforma. Você pode executar os exemplos de código que usamos neste artigo no Windows, no Linux ou no MacOS. 

Antes de começar, instale o seguinte software:

- **Linguagem de programação Go**: Os casos de teste do Terraform são escritos na linguagem [Go](https://golang.org/dl/).
- **dep**: [dep](https://github.com/golang/dep#installation) é uma ferramenta de gerenciamento de dependências para a linguagem Go.
- **CLI do Azure**: A [CLI do Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) é uma ferramenta de linha de comando que pode ser usada para gerenciar recursos do Azure. (O Terraform dá suporte à autenticação no Azure por meio de uma entidade de serviço ou [por meio da CLI do Azure](https://www.terraform.io/docs/providers/azurerm/authenticating_via_azure_cli.html).)
- **mage**: Usamos o [executável do Mage](https://github.com/magefile/mage/releases) para mostrar como simplificar a execução de casos do Terratest. 

## <a name="create-a-static-webpage-module"></a>Criar um módulo de página da Web estático

Neste tutorial, você cria um módulo do Terraform que provisiona uma página da Web estática carregando um único arquivo HTML para um blob do Armazenamento do Azure. Este módulo fornece a usuários em todo o mundo acesso à página da Web por meio de uma URL retornada pelo módulo.

> [!NOTE]
> Crie todos os arquivos que estão descritos nesta seção no local de seu [GOPATH](https://github.com/golang/go/wiki/SettingGOPATH).

Primeiro, crie uma nova pasta chamada `staticwebpage` na pasta `src` de seu GoPath. A estrutura de pastas geral deste tutorial é mostrada no exemplo a seguir. Os arquivos marcados com um asterisco `(*)` são o principal foco desta seção.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf      (*)
   ├ 📄 outputs.tf   (*)
   └ 📄 variables.tf (*)
```

O módulo de página da Web estática aceita três entradas. As entradas são declaradas em `./variables.tf`:

```hcl
variable "location" {
  description = "The Azure region in which to create all resources."
}

variable "website_name" {
  description = "The website name to use to create related resources in Azure."
}

variable "html_path" {
  description = "The file path of the static home page HTML in your local file system."
  default     = "index.html"
}
```

Conforme mencionado anteriormente neste artigo, este módulo também gera uma URL que é declarada em `./outputs.tf`:

```hcl
output "homepage_url" {
  value = azurerm_storage_blob.homepage.url
}
```

A lógica principal do módulo provisiona quatro recursos:
- **grupo de recursos**: O nome do grupo de recursos é a entrada `website_name` acrescida de `-staging-rg`.
- **conta de armazenamento**: O nome da conta de armazenamento é a entrada `website_name` acrescida de `data001`. Para atender às limitações de nome da conta de armazenamento, o módulo remove todos os caracteres especiais e usa letras minúsculas em todo o nome da conta de armazenamento.
- **contêiner de nome fixo**: O contêiner chama-se `wwwroot` e é criado na conta de armazenamento.
- **arquivo HTML único**: O arquivo HTML é lido na entrada `html_path` e carregado em `wwwroot/index.html`.

A lógica de módulo de página da Web estática é implementada em `./main.tf`:

```hcl
resource "azurerm_resource_group" "main" {
  name     = "${var.website_name}-staging-rg"
  location = var.location
}

resource "azurerm_storage_account" "main" {
  name                     = "${lower(replace(var.website_name, "/[[:^alnum:]]/", ""))}data001"
  resource_group_name      = azurerm_resource_group.main.name
  location                 = azurerm_resource_group.main.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_storage_container" "main" {
  name                  = "wwwroot"
  resource_group_name   = azurerm_resource_group.main.name
  storage_account_name  = azurerm_storage_account.main.name
  container_access_type = "blob"
}

resource "azurerm_storage_blob" "homepage" {
  name                   = "index.html"
  resource_group_name    = azurerm_resource_group.main.name
  storage_account_name   = azurerm_storage_account.main.name
  storage_container_name = azurerm_storage_container.main.name
  source                 = var.html_path
  type                   = "block"
  content_type           = "text/html"
}
```

### <a name="unit-test"></a>Teste de unidade

O Terratest foi projetado para testes de integração. Para essa finalidade, ele provisiona recursos reais em um ambiente real. Às vezes, os trabalhos de teste de integração podem se tornar excepcionalmente grandes, especialmente quando você tem um grande número de recursos para provisionar. A lógica que converte nomes de conta de armazenamento a que nos referimos na seção anterior é um bom exemplo. 

Mas não precisamos provisionar recursos. Queremos apenas ter certeza de que a lógica de conversão de nomenclatura está correta. Graças à flexibilidade do Terratest, podemos usar testes de unidade. Testes de unidade são casos de teste em execução localmente (embora seja necessário ter acesso à Internet). Os casos de teste de unidade executam comandos `terraform init` e `terraform plan` para analisar a saída de `terraform plan` e procurar os valores de atributos a serem comparados.

O restante desta seção descreve como usamos o Terratest para implementar um teste de unidade e ter certeza de que a lógica usada para converter os nomes das contas de armazenamento está correta. Estamos interessados somente nos arquivos marcados com um asterisco `(*)`.

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html                (*)
   │   │       └ 📄 main.tf                   (*)
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go (*)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Primeiro, usamos um arquivo HTML vazio chamado `./test/fixtures/storage-account-name/empty.html` como um espaço reservado.

O arquivo `./test/fixtures/storage-account-name/main.tf` é a moldura do caso de teste. Ele aceita uma entrada `website_name`, que também é a entrada dos testes de unidade. A lógica é mostrada aqui:

```hcl
variable "website_name" {
  description = "The name of your static website."
}

module "staticwebpage" {
  source       = "../../../"
  location     = "West US"
  website_name = var.website_name
  html_path    = "empty.html"
}
```

O componente principal é a implementação dos testes de unidade em `./test/storage_account_name_unit_test.go`.

Os desenvolvedores Go provavelmente notarão que o teste de unidade corresponde à assinatura de uma função de teste clássica do Go ao aceitar um argumento do tipo `*testing.T`.

No corpo do teste de unidade, temos um total de cinco casos definidos na variável `testCases` (`key` como entrada e `value` como saída esperada). Para cada caso de teste de unidade, primeiro executaremos `terraform init` focando na pasta de acessório de teste (`./test/fixtures/storage-account-name/`). 

Depois disso, um comando `terraform plan` que usa a entrada do caso de teste específica (veja a definição de `website_name` em `tfOptions`) salva o resultado em `./test/fixtures/storage-account-name/terraform.tfplan` (que não está listado na estrutura de pastas geral).

Esse arquivo de resultado é analisado como uma estrutura de código legível usando o analisador de plano oficial do Terraform.

Vamos procurar agora os atributos que nos interessam (nesse caso, o `name` da `azurerm_storage_account`) e comparar os resultados com a saída esperada:

```go
package test

import (
    "os"
    "path"
    "testing"

    "github.com/gruntwork-io/terratest/modules/terraform"
    terraformCore "github.com/hashicorp/terraform/terraform"
)

func TestUT_StorageAccountName(t *testing.T) {
    t.Parallel()

    // Test cases for storage account name conversion logic
    testCases := map[string]string{
        "TestWebsiteName": "testwebsitenamedata001",
        "ALLCAPS":         "allcapsdata001",
        "S_p-e(c)i.a_l":   "specialdata001",
        "A1phaNum321":     "a1phanum321data001",
        "E5e-y7h_ng":      "e5ey7hngdata001",
    }

    for input, expected := range testCases {
        // Specify the test case folder and "-var" options
        tfOptions := &terraform.Options{
            TerraformDir: "./fixtures/storage-account-name",
            Vars: map[string]interface{}{
                "website_name": input,
            },
        }

        // Terraform init and plan only
        tfPlanOutput := "terraform.tfplan"
        terraform.Init(t, tfOptions)
        terraform.RunTerraformCommand(t, tfOptions, terraform.FormatArgs(tfOptions, "plan", "-out="+tfPlanOutput)...)

        // Read and parse the plan output
        f, err := os.Open(path.Join(tfOptions.TerraformDir, tfPlanOutput))
        if err != nil {
            t.Fatal(err)
        }
        defer f.Close()
        plan, err := terraformCore.ReadPlan(f)
        if err != nil {
            t.Fatal(err)
        }

        // Validate the test result
        for _, mod := range plan.Diff.Modules {
            if len(mod.Path) == 2 && mod.Path[0] == "root" && mod.Path[1] == "staticwebpage" {
                actual := mod.Resources["azurerm_storage_account.main"].Attributes["name"].New
                if actual != expected {
                    t.Fatalf("Expect %v, but found %v", expected, actual)
                }
            }
        }
    }
}
```

Para executar os testes de unidade, conclua as etapas abaixo na linha de comando:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestUT_StorageAccountName
```

O resultado do teste Go tradicional é retornado em cerca de um minuto.

### <a name="integration-test"></a>Teste de integração

Ao contrário dos testes de unidade, os testes de integração precisam provisionar recursos para um ambiente real em uma perspectiva de ponta a ponta. O Terratest faz um bom trabalho com esse tipo de tarefa. 

As melhores práticas dos módulos do Terraform incluem a instalação da pasta `examples`. A pasta `examples` contém alguns exemplos de ponta a ponta. Para evitar trabalhar com dados reais, por que não testar esses exemplos como testes de integração? Nesta seção, abordaremos os três arquivos que estão marcados com um asterisco `(*)` na seguinte estrutura de pastas:

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html              (*)
   │       └ 📄 main.tf                 (*)
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go (*)
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Vamos começar com os exemplos. Uma nova pasta de exemplo chamada `hello-world/` é criada na pasta `./examples/`. Aqui, fornecemos uma página HTML simples para carregamento: `./examples/hello-world/index.html`.

```html
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>Hello World</title>
</head>
<body>
    <h1>Hi, Terraform Module</h1>
    <p>This is a sample web page to demonstrate Terratest.</p>
</body>
</html>
```

O exemplo de Terraform `./examples/hello-world/main.tf` é semelhante ao mostrado no teste de unidade. Há uma diferença relevante: o exemplo também imprime a URL do HTML carregado como uma página da Web denominada `homepage`.

```hcl
variable "website_name" {
  description = "The name of your static website."
  default     = "Hello-World"
}

module "staticwebpage" {
  source       = "../../"
  location     = "West US"
  website_name = var.website_name
}

output "homepage" {
  value = module.staticwebpage.homepage_url
}
```

Usamos o Terratest e a função de teste Go clássica novamente no arquivo de teste de integração `./test/hello_world_example_test.go`.

Ao contrário dos testes de unidade, os testes de integração criam recursos reais no Azure. É por isso que você precisa ter cuidado para evitar conflitos de nomenclatura. (Preste atenção principalmente em alguns nomes globalmente exclusivos, como nomes de conta de armazenamento.) Portanto, a primeira etapa da lógica do teste é gerar um `websiteName` aleatório usando a função `UniqueId()` fornecida pelo Terratest. Esta função gera um nome aleatório com letras minúsculas, letras maiúsculas ou números. `tfOptions` cria todos os comandos de Terraform que se destinam à pasta `./examples/hello-world/`. Ele também faz com que `website_name` seja definido como o `websiteName` aleatório.

Em seguida, `terraform init`, `terraform apply` e `terraform output` são executados individualmente. Usamos outra função auxiliar, `HttpGetWithCustomValidation()`, que é fornecida pelo Terratest. Usamos a função auxiliar para ter certeza de que o HTML é carregado para a URL de saída `homepage` que é retornada por `terraform output`. Podemos comparar o código de status HTTP GET com `200` e procurar algumas palavras-chave no conteúdo em HTML. Por fim, `terraform destroy` tem a “promessa” de ser executado aproveitando o recurso `defer` da linguagem Go.

```go
package test

import (
    "fmt"
    "strings"
    "testing"

    "github.com/gruntwork-io/terratest/modules/http-helper"
    "github.com/gruntwork-io/terratest/modules/random"
    "github.com/gruntwork-io/terratest/modules/terraform"
)

func TestIT_HelloWorldExample(t *testing.T) {
    t.Parallel()

    // Generate a random website name to prevent a naming conflict
    uniqueID := random.UniqueId()
    websiteName := fmt.Sprintf("Hello-World-%s", uniqueID)

    // Specify the test case folder and "-var" options
    tfOptions := &terraform.Options{
        TerraformDir: "../examples/hello-world",
        Vars: map[string]interface{}{
            "website_name": websiteName,
        },
    }

    // Terraform init, apply, output, and destroy
    defer terraform.Destroy(t, tfOptions)
    terraform.InitAndApply(t, tfOptions)
    homepage := terraform.Output(t, tfOptions, "homepage")

    // Validate the provisioned webpage
    http_helper.HttpGetWithCustomValidation(t, homepage, func(status int, content string) bool {
        return status == 200 &&
            strings.Contains(content, "Hi, Terraform Module") &&
            strings.Contains(content, "This is a sample web page to demonstrate Terratest.")
    })
}
```

Para executar os testes de integração, conclua as etapas abaixo na linha de comando:

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in test cases
cd test
go fmt
go test -run TestIT_HelloWorldExample
```

O resultado do teste Go tradicional é retornado em cerca de dois minutos. Você também pode executar testes de unidade e testes de integração executando estes comandos:

```shell
go fmt
go test
```

Os testes de integração levam muito mais tempo do que os testes de unidade (dois minutos para um caso de integração comparados com um minuto para cinco casos de unidade). Mas você é que decide se quer usar testes de unidade ou testes de integração em um cenário. Normalmente, preferimos usar testes de unidade para uma lógica complexa usando funções HCL do Terraform. Geralmente, podemos usar testes de integração para a perspectiva de ponta a ponta de um usuário.

## <a name="use-mage-to-simplify-running-terratest-cases"></a>Usar mage para simplificar casos do Terratest em execução 
A execução de casos de teste no Azure Cloud Shell requer a execução de comandos diferentes em vários diretórios. Para tornar esse processo mais eficiente, apresentamos o sistema de compilação em nosso projeto. Nesta seção, usaremos um sistema de compilação do Go, o mage, para fazer o trabalho.

A única coisa exigida pelo mage é o `magefile.go` no diretório raiz de seu projeto (marcado com `(+)` no exemplo abaixo):

```
 📁 GoPath/src/staticwebpage
   ├ 📁 examples
   │   └ 📁 hello-world
   │       ├ 📄 index.html
   │       └ 📄 main.tf
   ├ 📁 test
   │   ├ 📁 fixtures
   │   │   └ 📁 storage-account-name
   │   │       ├ 📄 empty.html
   │   │       └ 📄 main.tf
   │   ├ 📄 hello_world_example_test.go
   │   └ 📄 storage_account_name_unit_test.go
   ├ 📄 magefile.go (+)
   ├ 📄 main.tf
   ├ 📄 outputs.tf
   └ 📄 variables.tf
```

Aqui está um exemplo e `./magefile.go`. Nesse script de compilação, escrito em Go, implementamos cinco etapas de compilação:
- `Clean`: A etapa remove todos os arquivos gerados e temporários que são criados durante as execuções de teste.
- `Format`: A etapa executa `terraform fmt` e `go fmt` para formatar a base de código.
- `Unit`: A etapa executa todos os testes de unidade (usando a convenção de nomenclatura de função `TestUT_*`) na pasta `./test/`.
- `Integration`: A etapa é semelhante a `Unit`, mas em vez de testes de unidade, ela executa testes de integração (`TestIT_*`).
- `Full`: A etapa executa `Clean`, `Format`, `Unit` e `Integration` em sequência.

```go
// +build mage

// Build a script to format and run tests of a Terraform module project
package main

import (
    "fmt"
    "os"
    "path/filepath"

    "github.com/magefile/mage/mg"
    "github.com/magefile/mage/sh"
)

// The default target when the command executes `mage` in Cloud Shell
var Default = Full

// A build step that runs Clean, Format, Unit and Integration in sequence
func Full() {
    mg.Deps(Unit)
    mg.Deps(Integration)
}

// A build step that runs unit tests
func Unit() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running unit tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestUT_", "-v")
}

// A build step that runs integration tests
func Integration() error {
    mg.Deps(Clean)
    mg.Deps(Format)
    fmt.Println("Running integration tests...")
    return sh.RunV("go", "test", "./test/", "-run", "TestIT_", "-v")
}

// A build step that formats both Terraform code and Go code
func Format() error {
    fmt.Println("Formatting...")
    if err := sh.RunV("terraform", "fmt", "."); err != nil {
        return err
    }
    return sh.RunV("go", "fmt", "./test/")
}

// A build step that removes temporary build and test files
func Clean() error {
    fmt.Println("Cleaning...")
    return filepath.Walk(".", func(path string, info os.FileInfo, err error) error {
        if err != nil {
            return err
        }
        if info.IsDir() && info.Name() == "vendor" {
            return filepath.SkipDir
        }
        if info.IsDir() && info.Name() == ".terraform" {
            os.RemoveAll(path)
            fmt.Printf("Removed \"%v\"\n", path)
            return filepath.SkipDir
        }
        if !info.IsDir() && (info.Name() == "terraform.tfstate" ||
            info.Name() == "terraform.tfplan" ||
            info.Name() == "terraform.tfstate.backup") {
            os.Remove(path)
            fmt.Printf("Removed \"%v\"\n", path)
        }
        return nil
    })
}
```

Você pode usar os comandos a seguir para executar um conjunto de testes completo. O código é semelhante ao das etapas de execução usadas em uma seção anterior. 

```azurecli
az login    # Required when no service principal environment variables are present
```

```shell
cd [Your GoPath]/src/staticwebpage
dep init    # Run only once for this folder
dep ensure  # Required to run if you imported new packages in magefile or test cases
go fmt      # Only required when you change the magefile
mage
```

Você pode substituir a última linha de comando por etapas adicionais do mage. Por exemplo, você pode usar `mage unit` ou `mage clean`. É uma boa ideia incorporar comandos `dep` e `az login` no magefile. Não exibimos o código aqui. 

Com mage, você também pode compartilhar as etapas usando o sistema de pacotes do Go. Nesse caso, você pode simplificar magefiles entre todos os seus módulos fazendo referência apenas a uma implementação comum e declarando dependências (`mg.Deps()`).

**Opcional: Definir variáveis de ambiente da entidade de serviço para executar testes de aceitação**
 
Em vez de executar `az login` antes dos testes, você pode concluir a autenticação do Azure definindo as variáveis de ambiente da entidade de serviço. O Terraform publica [uma lista de nomes de variáveis de ambiente](https://www.terraform.io/docs/providers/azurerm/index.html#testing). (Somente as quatro primeiras dessas variáveis de ambiente são obrigatórias.) O Terraform também publica instruções detalhadas explicando como [obter o valor dessas variáveis de ambiente.](https://www.terraform.io/docs/providers/azurerm/authenticating_via_service_principal.html)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"] 
> [Página do GitHub Terratest](https://github.com/gruntwork-io/terratest).