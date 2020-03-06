---
title: Implantação de modelo What-if (visualização)
description: Determine quais alterações ocorrerão para seus recursos antes de implantar um modelo de Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: b9d4150779842614a5dc284a2b3a489593fabfe1
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78388529"
---
# <a name="resource-manager-template-deployment-what-if-operation-preview"></a>Operação What-If da implantação de modelo do Resource Manager (versão prévia)

Antes de implantar um modelo, talvez você queira Visualizar as alterações que ocorrerão. Azure Resource Manager fornece a operação What-If para permitir que você veja como os recursos serão alterados se você implantar o modelo. A operação What-If não faz nenhuma alteração nos recursos existentes. Em vez disso, ele prevê as alterações se o modelo especificado for implantado.

> [!NOTE]
> A operação What-If está atualmente em visualização. Para usá-lo, você deve [inscrever-se na versão prévia](https://aka.ms/armtemplatepreviews). Como uma versão de visualização, os resultados podem, às vezes, mostrar que um recurso será alterado quando, na verdade, nenhuma alteração ocorrerá. Estamos trabalhando para reduzir esses problemas, mas precisamos de sua ajuda. Informe esses problemas em [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Você pode usar a operação What-If com os comandos do PowerShell ou com as operações da API REST.

No PowerShell, a saída inclui resultados codificados por cor que ajudam você a ver os diferentes tipos de alterações.

![Implantação do modelo do Resource Manager e-se operação fullresourcepayload e tipos de alteração](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

O texto Ouptput é:

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

Você pode usar o Azure PowerShell ou a API REST do Azure para a operação What-If.

### <a name="azure-powershell"></a>Azure PowerShell

Para ver uma visualização das alterações antes de implantar um modelo, adicione o parâmetro de opção `-Whatif` ao comando de implantação.

* `New-AzResourceGroupDeployment -Whatif` para implantações de grupo de recursos
* `New-AzSubscriptionDeployment -Whatif` e `New-AzDeployment -Whatif` para implantações em nível de assinatura

Ou, você pode usar o parâmetro de opção `-Confirm` para visualizar as alterações e receber uma solicitação para continuar com a implantação.

* `New-AzResourceGroupDeployment -Confirm` para implantações de grupo de recursos
* `New-AzSubscriptionDeployment -Confirm` e `New-AzDeployment -Confirm` para implantações em nível de assinatura

Os comandos anteriores retornam um resumo de texto que você pode inspecionar manualmente. Para obter um objeto que você pode inspecionar para alterações programaticamente, use:

* `$results = Get-AzResourceGroupDeploymentWhatIf` para implantações de grupo de recursos
* `$results = Get-AzSubscriptionDeploymentWhatIf` ou `$results = Get-AzDeploymentWhatIf` para implantações em nível de assinatura

> [!NOTE]
> Antes do lançamento da versão 2.0.1-alpha5, você usou o comando `New-AzDeploymentWhatIf`. Esse comando foi substituído pelos comandos `Get-AzDeploymentWhatIf`, `Get-AzResourceGroupDeploymentWhatIf`e `Get-AzSubscriptionDeploymentWhatIf`. Se você usou uma versão anterior, precisará atualizar essa sintaxe. O parâmetro `-ScopeType` foi removido.

### <a name="azure-rest-api"></a>API REST do Azure

Para a API REST, use:

* [Implantações-What If](/rest/api/resources/deployments/whatif) para implantações de grupo de recursos
* [Implantações-What If no escopo da assinatura](/rest/api/resources/deployments/whatifatsubscriptionscope) para implantações em nível de assinatura

## <a name="change-types"></a>Alterar tipos

A operação What-If lista seis tipos diferentes de alterações:

- **Criar**: o recurso não existe atualmente, mas está definido no modelo. O recurso será criado.

- **Excluir**: esse tipo de alteração se aplica somente ao usar o [modo completo](deployment-modes.md) para implantação. O recurso existe, mas não está definido no modelo. Com o modo completo, o recurso será excluído. Somente os recursos que [dão suporte à exclusão de modo completo](complete-mode-deletion.md) são incluídos nesse tipo de alteração.

- **Ignorar**: o recurso existe, mas não está definido no modelo. O recurso não será implantado ou modificado.

- **NoChange**: o recurso existe e é definido no modelo. O recurso será reimplantado, mas as propriedades do recurso não serão alteradas. Esse tipo de alteração é retornado quando [ResultFormat](#result-format) é definido como `FullResourcePayloads`, que é o valor padrão.

- **Modify**: o recurso existe e é definido no modelo. O recurso será reimplantado e as propriedades do recurso serão alteradas. Esse tipo de alteração é retornado quando [ResultFormat](#result-format) é definido como `FullResourcePayloads`, que é o valor padrão.

- **Implantar**: o recurso existe e é definido no modelo. O recurso será reimplantado. As propriedades do recurso podem ou não ser alteradas. A operação retorna esse tipo de alteração quando não tem informações suficientes para determinar se as propriedades serão alteradas. Você só verá essa condição quando [ResultFormat](#result-format) estiver definido como `ResourceIdOnly`.

## <a name="result-format"></a>Formato do resultado

Você pode controlar o nível de detalhe que é retornado sobre as alterações previstas. Nos comandos de implantação (`New-Az*Deployment`), use o parâmetro **-WhatIfResultFormat** . Nos comandos de objeto programático (`Get-Az*DeploymentWhatIf`), use o parâmetro **ResultFormat** .

Defina o parâmetro format como **FullResourcePayloads** para obter uma lista de recursos que serão alterados e detalhes sobre as propriedades que serão alteradas. Defina o parâmetro format como **ResourceIdOnly** para obter uma lista de recursos que serão alterados. O valor padrão é **FullResourcePayloads**.  

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

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modificação de teste

Após a conclusão da implantação, você estará pronto para testar a operação What-If. Desta vez, implante um [modelo que altere a rede virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Não há uma marca original, uma sub-rede foi removida e o prefixo de endereço foi alterado.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

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

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
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

## <a name="confirm-deletion"></a>Confirmar exclusão

A operação What-If dá suporte ao uso do [modo de implantação](deployment-modes.md). Quando definido para o modo completo, os recursos que não estão no modelo são excluídos. O exemplo a seguir implanta um [modelo que não tem recursos definidos](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) no modo completo.

Para visualizar as alterações antes de implantar um modelo, use o parâmetro de opção `-Confirm` com o comando de implantação. Se as alterações forem as esperadas, confirme se deseja que a implantação seja concluída.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

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

- Se você observar resultados incorretos da versão de visualização de What-If, informe os problemas em [https://aka.ms/whatifissues](https://aka.ms/whatifissues).
- Para implantar modelos com Azure PowerShell, consulte [implantar recursos com modelos do Resource Manager e Azure PowerShell](deploy-powershell.md).
- Para implantar modelos com REST, consulte [implantar recursos com modelos do Resource Manager e a API REST do Gerenciador de recursos](deploy-rest.md).
