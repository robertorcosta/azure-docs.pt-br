---
title: Kit de ferramentas de teste de modelo ARM
description: Descreve como executar o kit de ferramentas de teste do modelo ARM em seu modelo. O kit de ferramentas permite que você veja se implementou as práticas recomendadas.
ms.topic: conceptual
ms.date: 06/19/2020
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: 7b88096dfdd1c7fb3e2671a369132e75a8885b8d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255706"
---
# <a name="use-arm-template-test-toolkit"></a>Usar o kit de ferramentas de teste do modelo ARM

O [Kit de ferramentas de teste do modelo ARM](https://aka.ms/arm-ttk) verifica se o modelo usa práticas recomendadas. Quando o modelo não está em conformidade com as práticas recomendadas, ele retorna uma lista de avisos com as alterações sugeridas. Usando o kit de ferramentas de teste, você pode aprender a evitar problemas comuns no desenvolvimento de modelos.

O kit de ferramentas de teste fornece um [conjunto de testes padrão](test-cases.md). Esses testes são recomendações, mas não requisitos. Você pode decidir quais testes são relevantes para suas metas e personalizar quais testes são executados.

Este artigo descreve como executar o kit de ferramentas de teste e como adicionar ou remover testes. Para obter descrições dos testes padrão, consulte [casos de teste do Toolkit](test-cases.md).

O kit de ferramentas é um conjunto de scripts do PowerShell que pode ser executado a partir de um comando no PowerShell ou na CLI.

## <a name="download-test-toolkit"></a>Baixar o kit de ferramentas de teste

Para usar o kit de ferramentas de teste, você pode criar uma bifurcação e clonar o [repositório](https://aka.ms/arm-ttk) que contém os scripts ou [baixar o arquivo. zip mais recente](https://aka.ms/arm-ttk-latest).

Dependendo da política de execução do computador em que você executa o script, você poderá receber um erro sobre a execução de scripts da Internet. Você precisa alterar a política de [execução](/powershell/module/microsoft.powershell.core/about/about_execution_policies) ou [desbloquear os arquivos de script](/powershell/module/microsoft.powershell.utility/unblock-file).

## <a name="run-on-powershell"></a>Executar no PowerShell

Antes de executar os testes, importe o módulo.

```powershell
Import-Module .\arm-ttk.psd1 # from the same directory as .\arm-ttk.psd1
```

Para executar os testes no **PowerShell**, use o seguinte comando:

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

## <a name="run-on-linux"></a>Executar no Linux

Antes de executar os testes, instale o [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-linux).

Para executar os testes no **Linux** no bash, use o seguinte comando:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

Você também pode executar o teste em pwsh.exe.

## <a name="run-on-macos"></a>Executar no macOS

Antes de executar os testes, instale o [PowerShell Core](/powershell/scripting/install/installing-powershell-core-on-macos). 

Instale `coreutils`:

```bash
brew install coreutils
```

Para executar os testes no **MacOS**, use o seguinte comando:

```bash
Test-AzTemplate.sh -TemplatePath $TemplateFolder
```

## <a name="result-format"></a>Formato de resultado

Os testes que são aprovados são exibidos em **verde** e precedido com **[+]**.

Os testes que falham são exibidos em **vermelho** e precedido com **[-]**.

:::image type="content" source="./media/template-test-toolkit/view-results.png" alt-text="exibir resultados de teste":::

Os resultados do texto são:

```powershell
[+] adminUsername Should Not Be A Literal (24 ms)
[+] apiVersions Should Be Recent (18 ms)
[+] artifacts parameter (16 ms)
[+] DeploymentTemplate Schema Is Correct (17 ms)
[+] IDs Should Be Derived From ResourceIDs (15 ms)
[-] Location Should Not Be Hardcoded (41 ms)
     azuredeploy.json must use the location parameter, not resourceGroup().location (except when used as a default value in the main template)
```

## <a name="test-parameters"></a>Parâmetros de teste

Quando você fornece o parâmetro **-TemplatePath** , o kit de ferramentas examina a pasta em busca de um modelo chamado azuredeploy.jsno ou maintemplate.js. Ele testa primeiro esse modelo e, em seguida, testa todos os outros modelos na pasta e suas subpastas. Os outros modelos são testados como modelos vinculados. Se o caminho incluir um arquivo chamado [CreateUiDefinition.jsem](../managed-applications/create-uidefinition-overview.md), ele executará testes que são relevantes para a definição da interface do usuário.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder
```

Para testar um arquivo nessa pasta, adicione o parâmetro **-File** . No entanto, a pasta ainda deve ter um modelo principal chamado azuredeploy.jsno ou maintemplate.jsem.

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -File cdn.json
```

Por padrão, todos os testes são executados. Para especificar testes individuais a serem executados, use o parâmetro **-Test** . Forneça o nome do teste. Para obter os nomes, consulte [casos de teste para o kit de ferramentas](test-cases.md).

```powershell
Test-AzTemplate -TemplatePath $TemplateFolder -Test "Resources Should Have Location"
```

## <a name="customize-tests"></a>Personalizar testes

Para modelos de ARM, o kit de ferramentas executa todos os testes na pasta **\arm-ttk\testcases\deploymentTemplate**. Se você quiser remover permanentemente um teste, exclua esse arquivo da pasta.

Para arquivos [CreateUiDefinition](../managed-applications/create-uidefinition-overview.md) , ele executa todos os testes na pasta **\arm-ttk\testcases\CreateUiDefinition**.

Para adicionar seu próprio teste, crie um arquivo com a Convenção de nomenclatura: **Your-Custom-Test-Name.test.ps1**.

O teste pode obter o modelo como um parâmetro de objeto ou um parâmetro de cadeia de caracteres. Normalmente, você usa um ou outro, mas pode usar ambos.

Use o parâmetro Object quando precisar obter uma seção do modelo e iterar por meio de suas propriedades. Um teste que usa o parâmetro Object tem o seguinte formato:

```powershell
param(
    [Parameter(Mandatory=$true,Position=0)]
    [PSObject]
    $TemplateObject
)

# Implement test logic that evaluates parts of the template.
# Output error with: Write-Error -Message
```

O objeto de modelo tem as seguintes propriedades:

* $schema
* contentVersion
* parâmetros
* variáveis
* recursos
* outputs

Por exemplo, você pode obter a coleção de parâmetros com `$TemplateObject.parameters` .

Use o parâmetro de cadeia de caracteres quando precisar executar uma operação de cadeia de caracteres em todo o modelo. Um teste que usa o parâmetro de cadeia de caracteres tem o seguinte formato:

```powershell
param(
    [Parameter(Mandatory)]
    [string]
    $TemplateText
)

# Implement test logic that performs string operations.
# Output error with: Write-Error -Message
```

Por exemplo, você pode executar uma expressão regular do parâmetro de cadeia de caracteres para ver se uma sintaxe específica é usada.

Para saber mais sobre como implementar o teste, examine os outros testes nessa pasta.

## <a name="integrate-with-azure-pipelines"></a>Integrar com o Azure Pipelines

Você pode adicionar o kit de ferramentas de teste ao seu pipeline do Azure. Com um pipeline, você pode executar o teste toda vez que o modelo for atualizado ou executá-lo como parte do processo de implantação.

A maneira mais fácil de adicionar o kit de ferramentas de teste ao seu pipeline é com extensões de terceiros. As duas extensões a seguir estão disponíveis:

* [Executar testes de TTK ARM](https://marketplace.visualstudio.com/items?itemName=Sam-Cogan.ARMTTKExtension)
* [Testador de modelo ARM](https://marketplace.visualstudio.com/items?itemName=maikvandergaag.maikvandergaag-arm-ttk)

Ou, você pode implementar suas próprias tarefas. O exemplo a seguir mostra como baixar o kit de ferramentas de teste.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": false,
    "alwaysRun": false,
    "displayName": "Download TTK",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "New-Item '$(ttk.folder)' -ItemType Directory\nInvoke-WebRequest -uri '$(ttk.uri)' -OutFile \"$(ttk.folder)/$(ttk.asset.filename)\" -Verbose\nGet-ChildItem '$(ttk.folder)' -Recurse\n\nWrite-Host \"Expanding files...\"\nExpand-Archive -Path '$(ttk.folder)/*.zip' -DestinationPath '$(ttk.folder)' -Verbose\n\nWrite-Host \"Expanded files found:\"\nGet-ChildItem '$(ttk.folder)' -Recurse",
        "errorActionPreference": "stop",
        "failOnStderr": "false",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

O exemplo a seguir mostra como executar os testes.

```json
{
    "environment": {},
    "enabled": true,
    "continueOnError": true,
    "alwaysRun": false,
    "displayName": "Run Best Practices Tests",
    "timeoutInMinutes": 0,
    "condition": "succeeded()",
    "task": {
        "id": "e213ff0f-5d5c-4791-802d-52ea3e7be1f1",
        "versionSpec": "2.*",
        "definitionType": "task"
    },
    "inputs": {
        "targetType": "inline",
        "filePath": "",
        "arguments": "",
        "script": "Import-Module $(ttk.folder)/arm-ttk/arm-ttk.psd1 -Verbose\n$testOutput = @(Test-AzTemplate -TemplatePath \"$(sample.folder)\")\n$testOutput\n\nif ($testOutput | ? {$_.Errors }) {\n   exit 1 \n} else {\n    Write-Host \"##vso[task.setvariable variable=result.best.practice]$true\"\n    exit 0\n} \n",
        "errorActionPreference": "continue",
        "failOnStderr": "true",
        "ignoreLASTEXITCODE": "false",
        "pwsh": "true",
        "workingDirectory": ""
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os testes padrão, consulte [casos de teste para o kit de ferramentas](test-cases.md).
