---
title: Implantação de modelo What-if (visualização)
description: Determine quais alterações ocorrerão para seus recursos antes de implantar um modelo de Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 04/28/2020
ms.author: jgao
ms.openlocfilehash: f13789912e5b801295f1f926a12db50849cd75d8
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82509577"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Operação de hipóteses de implantação de modelo ARM (versão prévia)

Antes de implantar um modelo de Azure Resource Manager (ARM), você pode visualizar as alterações que ocorrerão. Azure Resource Manager fornece a operação What-If para permitir que você veja como os recursos serão alterados se você implantar o modelo. A operação What-If não faz nenhuma alteração nos recursos existentes. Em vez disso, ele prevê as alterações se o modelo especificado for implantado.

> [!NOTE]
> A operação What-If está atualmente em visualização. Como uma versão de visualização, os resultados podem, às vezes, mostrar que um recurso será alterado quando, na verdade, nenhuma alteração ocorrerá. Estamos trabalhando para reduzir esses problemas, mas precisamos de sua ajuda. Informe esses problemas em [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Você pode usar a operação What-If com as operações Azure PowerShell, CLI do Azure ou API REST.

## <a name="install-powershell-module"></a>Instalar o módulo do PowerShell

Para usar What-If no PowerShell, você deve instalar uma versão de visualização do módulo AZ. Resources da galeria do PowerShell. Mas, antes de instalar o módulo, verifique se você tem o PowerShell Core (6. x ou 7. x). Se você tiver o PowerShell 5. x ou anterior, [Atualize sua versão do PowerShell](/powershell/scripting/install/installing-powershell). Você não pode instalar o módulo de visualização no PowerShell 5. x ou anterior.

### <a name="install-preview-version"></a>Instalar versão de visualização

Para instalar o módulo de visualização, use:

```powershell
Install-Module Az.Resources -RequiredVersion 1.12.1-preview -AllowPrerelease
```

### <a name="uninstall-alpha-version"></a>Desinstalar a versão Alpha

Se você já tiver instalado uma versão Alpha do módulo What-If, desinstale esse módulo. A versão Alpha só estava disponível para usuários que se inscreveram para uma visualização antecipada. Se você não instalou essa versão prévia, poderá ignorar esta seção.

1. Execute o PowerShell como administrador
1. Verifique as versões instaladas do módulo AZ. Resources.

   ```powershell
   Get-InstalledModule -Name Az.Resources -AllVersions | select Name,Version
   ```

1. Se você tiver uma versão instalada com um número de versão no formato **2. x. x-Alpha**, desinstale essa versão.

   ```powershell
   Uninstall-Module Az.Resources -RequiredVersion 2.0.1-alpha5 -AllowPrerelease
   ```

1. Cancele o registro do repositório What-If usado para instalar a versão prévia.

   ```powershell
   Unregister-PSRepository -Name WhatIfRepository
   ```

Você está pronto para usar o quê.

## <a name="install-azure-cli-module"></a>Instalar o módulo CLI do Azure

Para usar o What-If no CLI do Azure, você deve ter CLI do Azure 2.5.0 ou posterior. Se necessário, [Instale a versão mais recente do CLI do Azure](/cli/azure/install-azure-cli).

## <a name="see-results"></a>Ver resultados

Quando você usa What-If no PowerShell ou CLI do Azure, a saída inclui resultados codificados por cor que ajudam você a ver os diferentes tipos de alterações.

![Implantação do modelo do Resource Manager e-se operação fullresourcepayload e tipos de alteração](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A saída de texto é:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

## <a name="what-if-commands"></a>Comandos What-If

### <a name="azure-powershell"></a>Azure PowerShell

Para visualizar as alterações antes de implantar um modelo, adicione `-Whatif` o parâmetro de opção ao comando de implantação.

* `New-AzResourceGroupDeployment -Whatif`para implantações de grupo de recursos
* `New-AzSubscriptionDeployment -Whatif`e `New-AzDeployment -Whatif` para implantações em nível de assinatura

Ou, você pode usar o `-Confirm` parâmetro switch para visualizar as alterações e receber uma solicitação para continuar com a implantação.

* `New-AzResourceGroupDeployment -Confirm`para implantações de grupo de recursos
* `New-AzSubscriptionDeployment -Confirm`e `New-AzDeployment -Confirm` para implantações em nível de assinatura

Os comandos anteriores retornam um resumo de texto que você pode inspecionar manualmente. Para obter um objeto que você pode inspecionar para alterações programaticamente, use:

* `$results = Get-AzResourceGroupDeploymentWhatIfResult`para implantações de grupo de recursos
* `$results = Get-AzSubscriptionDeploymentWhatIfResult`ou `$results = Get-AzDeploymentWhatIfResult` para implantações de nível de assinatura

### <a name="azure-cli"></a>CLI do Azure

Para visualizar as alterações antes de implantar um modelo, `what-if` use com o comando de implantação.

* `az deployment group what-if`para implantações de grupo de recursos
* `az deployment sub what-if`para implantações em nível de assinatura

Ou, você pode usar o `--confirm-with-what-if` parâmetro para visualizar as alterações e receber uma solicitação para continuar com a implantação.

* `az deployment group create --confirm-with-what-if`para implantações de grupo de recursos
* `az deployment sub create --confirm-with-what-if`para implantações em nível de assinatura

Os comandos anteriores retornam um resumo de texto que você pode inspecionar manualmente. Para obter um objeto JSON que você pode inspecionar para alterações programaticamente, use:

* `az deployment group what-if --no-pretty-print`para implantações de grupo de recursos
* `az deployment sub what-if --no-pretty-print`para implantações em nível de assinatura

### <a name="azure-rest-api"></a>API REST do Azure

Para a API REST, use:

* [Implantações-What If](/rest/api/resources/deployments/whatif) para implantações de grupo de recursos
* [Implantações-What If no escopo da assinatura](/rest/api/resources/deployments/whatifatsubscriptionscope) para implantações em nível de assinatura

## <a name="change-types"></a>Alterar tipos

A operação What-If lista seis tipos diferentes de alterações:

- **Criar**: o recurso não existe atualmente, mas está definido no modelo. O recurso será criado.

- **Excluir**: esse tipo de alteração se aplica somente ao usar o [modo completo](deployment-modes.md) para implantação. O recurso existe, mas não está definido no modelo. Com o modo completo, o recurso será excluído. Somente os recursos que [dão suporte à exclusão de modo completo](complete-mode-deletion.md) são incluídos nesse tipo de alteração.

- **Ignorar**: o recurso existe, mas não está definido no modelo. O recurso não será implantado ou modificado.

- **NoChange**: o recurso existe e é definido no modelo. O recurso será reimplantado, mas as propriedades do recurso não serão alteradas. Esse tipo de alteração é retornado quando [ResultFormat](#result-format) é definido `FullResourcePayloads`como, que é o valor padrão.

- **Modify**: o recurso existe e é definido no modelo. O recurso será reimplantado e as propriedades do recurso serão alteradas. Esse tipo de alteração é retornado quando [ResultFormat](#result-format) é definido `FullResourcePayloads`como, que é o valor padrão.

- **Implantar**: o recurso existe e é definido no modelo. O recurso será reimplantado. As propriedades do recurso podem ou não ser alteradas. A operação retorna esse tipo de alteração quando não tem informações suficientes para determinar se as propriedades serão alteradas. Você só verá essa condição quando [ResultFormat](#result-format) estiver definido como `ResourceIdOnly`.

## <a name="result-format"></a>Formato de resultado

Você controla o nível de detalhe que é retornado sobre as alterações previstas. Você tem duas opções:

* **FullResourcePayloads** -retorna uma lista de recursos que serão alterados e detalhes sobre as propriedades que serão alteradas
* **ResourceIdOnly** -retorna uma lista de recursos que serão alterados

O valor padrão é **FullResourcePayloads**.

Para comandos de implantação do PowerShell, `-WhatIfResultFormat` use o parâmetro. Nos comandos de objeto programático, use `ResultFormat` o parâmetro.

CLI do Azure, use o `--result-format` parâmetro.
 
Os resultados a seguir mostram os dois formatos de saída diferentes:

- Cargas de recursos completas

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Somente ID do recurso

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Executar a operação What-If

### <a name="set-up-environment"></a>Configurar o ambiente

Para ver como funciona o que acontece, vamos executar alguns testes. Primeiro, implante um [modelo que cria uma rede virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Você usará essa rede virtual para testar como as alterações são relatadas por What-If.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Modificação de teste

Após a conclusão da implantação, você estará pronto para testar a operação What-If. Desta vez, você implanta um [modelo que altera a rede virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Falta uma marca original nas marcas originais, uma sub-rede foi removida e o prefixo de endereço foi alterado.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

A saída What-If é semelhante a:

![Saída da operação What-If da implantação de modelo do Resource Manager](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

A saída de texto é:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Observe na parte superior da saída que as cores são definidas para indicar o tipo de alterações.

Na parte inferior da saída, mostra que o proprietário da marca foi excluído. O prefixo de endereço mudou de 10.0.0.0/16 para 10.0.0.0/15. A sub-rede denominada subnet001 foi excluída. Lembre-se de que essas alterações não foram realmente implantadas. Você verá uma visualização das alterações que ocorrerão se você implantar o modelo.

Algumas das propriedades listadas como excluídas não serão realmente alteradas. As propriedades podem ser relatadas incorretamente como excluídas quando não estão no modelo, mas são definidas automaticamente durante a implantação como valores padrão. Esse resultado é considerado "ruído" na resposta What-If. O recurso implantado final terá os valores definidos para as propriedades. Como a operação What-If amadurece, essas propriedades serão filtradas do resultado.

## <a name="programmatically-evaluate-what-if-results"></a>Avaliar programaticamente os resultados de hipóteses

Agora, vamos avaliar programaticamente os resultados What-If definindo o comando como uma variável.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Você pode ver um resumo de cada alteração.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Confirmar exclusão

A operação What-If dá suporte ao uso do [modo de implantação](deployment-modes.md). Quando definido para o modo completo, os recursos que não estão no modelo são excluídos. O exemplo a seguir implanta um [modelo que não tem recursos definidos](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) no modo completo.

Para visualizar as alterações antes de implantar um modelo, use `-Confirm` o parâmetro switch com o comando de implantação. Se as alterações forem as esperadas, confirme se deseja que a implantação seja concluída.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Como nenhum recurso está definido no modelo e o modo de implantação está definido como concluído, a rede virtual será excluída.

![Implantação do modelo do Resource Manager o modo de implantação de saída de operação de conclusão](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

A saída de texto é:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001"
      location:        "centralus"
      name:            "vnet-001"
      tags.CostCenter: "12345"
      tags.Owner:      "Team A"
      type:            "Microsoft.Network/virtualNetworks"

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "Y"):
```

Você vê as alterações esperadas e pode confirmar que deseja que a implantação seja executada.

## <a name="next-steps"></a>Próximas etapas

- Se você observar resultados incorretos da versão de visualização de What-If, informe os problemas [https://aka.ms/whatifissues](https://aka.ms/whatifissues)em.
- Para implantar modelos com Azure PowerShell, consulte [implantar recursos com modelos ARM e Azure PowerShell](deploy-powershell.md).
- Para implantar modelos com CLI do Azure, consulte [implantar recursos com modelos ARM e CLI do Azure](deploy-cli.md).
- Para implantar modelos com REST, consulte [implantar recursos com modelos ARM e API REST do Gerenciador de recursos](deploy-rest.md).
