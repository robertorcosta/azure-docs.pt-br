---
title: Implantação de modelo e se (Visualização)
description: Determine quais alterações acontecerão com seus recursos antes de implantar um modelo do Azure Resource Manager.
author: mumian
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: jgao
ms.openlocfilehash: bc42585204e5cc2c3ece5293a3934fd22fe8507b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156439"
---
# <a name="arm-template-deployment-what-if-operation-preview"></a>Implantação do modelo ARM (Visualização)

Antes de implantar um modelo ARM (Azure Resource Manager, gerenciador de recursos do Azure), você pode querer visualizar as alterações que acontecerão. O Azure Resource Manager fornece a operação e se para que você veja como os recursos serão diferentes se você implantar o modelo. A operação e se não faz nenhuma alteração nos recursos existentes. Em vez disso, ele prevê as alterações se o modelo especificado for implantado.

> [!NOTE]
> A operação e se está atualmente em pré-visualização. Para usá-lo, você deve [inscrever-se na versão prévia](https://aka.ms/armtemplatepreviews). Como uma versão de pré-visualização, os resultados podem às vezes mostrar que um recurso mudará quando, na verdade, nenhuma alteração acontecerá. Estamos trabalhando para reduzir esses problemas, mas precisamos de sua ajuda. Por favor, reporte essas questões em [https://aka.ms/whatifissues](https://aka.ms/whatifissues).

Você pode usar a operação "e se" com os comandos PowerShell ou operações de API REST.

No PowerShell, a saída inclui resultados codificados por cores que ajudam você a ver os diferentes tipos de alterações.

![Implantação do modelo do Resource Manager de implantação de pacotes de recursos completos e tipos de alteração](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

O texto ouptput é:

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

## <a name="what-if-commands"></a>Comandos e se

Você pode usar o Azure PowerShell ou o Azure REST API para a operação what-if.

### <a name="azure-powershell"></a>Azure PowerShell

Para ver uma visualização das alterações antes `-Whatif` de implantar um modelo, adicione o parâmetro switch ao comando de implantação.

* `New-AzResourceGroupDeployment -Whatif`para implantações de grupos de recursos
* `New-AzSubscriptionDeployment -Whatif`e `New-AzDeployment -Whatif` para implantações de nível de assinatura

Ou, você pode `-Confirm` usar o parâmetro do switch para visualizar as alterações e ser solicitado a continuar com a implantação.

* `New-AzResourceGroupDeployment -Confirm`para implantações de grupos de recursos
* `New-AzSubscriptionDeployment -Confirm`e `New-AzDeployment -Confirm` para implantações de nível de assinatura

Os comandos anteriores retornam um resumo de texto que você pode inspecionar manualmente. Para obter um objeto que você pode inspecionar programáticamente para alterações, use:

* `$results = Get-AzResourceGroupDeploymentWhatIf`para implantações de grupos de recursos
* `$results = Get-AzSubscriptionDeploymentWhatIf`ou `$results = Get-AzDeploymentWhatIf` para implantações de nível de assinatura

> [!NOTE]
> Antes do lançamento da versão 2.0.1-alfa5, você usou o `New-AzDeploymentWhatIf` comando. Este comando foi substituído `Get-AzDeploymentWhatIf`pelos `Get-AzResourceGroupDeploymentWhatIf`comandos e `Get-AzSubscriptionDeploymentWhatIf` comandos. Se você usou uma versão anterior, você precisa atualizar essa sintaxe. O `-ScopeType` parâmetro foi removido.

### <a name="azure-rest-api"></a>API REST do Azure

Para a API REST, use:

* [Implantações - E se](/rest/api/resources/deployments/whatif) para implantações de grupos de recursos
* [Implantações - E se no escopo de assinatura](/rest/api/resources/deployments/whatifatsubscriptionscope) para implantações de nível de assinatura

## <a name="change-types"></a>Alterar tipos

A operação e se lista seis tipos diferentes de alterações:

- **Criar**: O recurso não existe atualmente, mas é definido no modelo. O recurso será criado.

- **Excluir**: Este tipo de alteração só se aplica quando se usa o [modo completo](deployment-modes.md) para implantação. O recurso existe, mas não está definido no modelo. Com o modo completo, o recurso será excluído. Apenas os recursos que [suportam a exclusão completa do modo](complete-mode-deletion.md) estão incluídos neste tipo de alteração.

- **Ignore**: O recurso existe, mas não está definido no modelo. O recurso não será implantado ou modificado.

- **NoChange**: O recurso existe e é definido no modelo. O recurso será reimplantado, mas as propriedades do recurso não serão alterar. Esse tipo de alteração é retornado `FullResourcePayloads`quando [resultformat](#result-format) é definido para , que é o valor padrão.

- **Modificar**: O recurso existe e é definido no modelo. O recurso será reimplantado e as propriedades do recurso serão alteradas. Esse tipo de alteração é retornado `FullResourcePayloads`quando [resultformat](#result-format) é definido para , que é o valor padrão.

- **Implantar**: O recurso existe e é definido no modelo. O recurso será reimplantado. As propriedades do recurso podem ou não mudar. A operação retorna esse tipo de alteração quando não tem informações suficientes para determinar se alguma propriedade será mudada. Você só vê essa condição quando `ResourceIdOnly`o Formato de [Resultado](#result-format) é definido como .

## <a name="result-format"></a>Formato de resultado

Você pode controlar o nível de detalhes que é devolvido sobre as mudanças previstas. Nos comandos de`New-Az*Deployment`implantação (), use o parâmetro **-WhatIfResultFormat.** Nos comandos de objeto`Get-Az*DeploymentWhatIf`programático (), use o parâmetro **ResultFormat.**

Defina o parâmetro de formato no **FullResourcePayloads** para obter uma lista de recursos que serão alterar e detalhes sobre as propriedades que serão alteradas. Defina o parâmetro de formato para **ResourceIdSomente** para obter uma lista de recursos que serão alterar. O valor padrão é **FullResourcePayloads**.  

Os resultados a seguir mostram os dois formatos de saída diferentes:

- Cargas completas de recursos

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

- Somente iD de recursos

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Executar operação e se

### <a name="set-up-environment"></a>Configurar o ambiente

Para ver como o "e-if" funciona, vamos fazer alguns testes. Primeiro, implante um [modelo que crie uma rede virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Você usará esta rede virtual para testar como as alterações são relatadas pelo "e-if".

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

### <a name="test-modification"></a>Modificação de teste

Depois que a implantação for concluída, você está pronto para testar a operação "e se". Desta vez, implante um [modelo que altera a rede virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Está faltando uma das tags originais, uma sub-rede foi removida e o prefixo de endereço foi alterado.

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

A saída e-if parece semelhante a:

![Implantação do modelo de gerenciamento de recursos e se a saída de operação](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

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

Na parte inferior da saída, mostra que a tag Owner foi excluída. O prefixo de endereço passou de 10.0.0.0/16 para 10.0.0.0/15. A sub-rede chamada subnet001 foi excluída. Lembre-se que essas mudanças não foram realmente implantadas. Você vê uma visualização das alterações que acontecerão se você implantar o modelo.

Algumas das propriedades listadas como excluídas não serão realmente alteradas. As propriedades podem ser incorretamente relatadas como excluídas quando não estão no modelo, mas são automaticamente definidas durante a implantação como valores padrão. Este resultado é considerado "ruído" na resposta "e se". O recurso final implantado terá os valores definidos para as propriedades. À medida que a operação e se amadurecer, essas propriedades serão filtradas do resultado.

## <a name="programmatically-evaluate-what-if-results"></a>Avalie programáticamente os resultados de e-if

Agora, vamos avaliar programáticamente os resultados do "e se" definindo o comando como uma variável.

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIf `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Você pode ver um resumo de cada mudança.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

## <a name="confirm-deletion"></a>Confirmar exclusão

A operação e se suporta usando o [modo de implantação](deployment-modes.md). Quando definido para completar o modo, os recursos que não estão no modelo são excluídos. O exemplo a seguir implanta um [modelo que não tem recursos definidos](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) no modo completo.

Para visualizar alterações antes de `-Confirm` implantar um modelo, use o parâmetro switch com o comando deployment. Se as alterações forem como você esperava, confirme se deseja que a implantação seja concluída.

```azurepowershell
New-AzResourceGroupDeployment `
  -Confirm `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json" `
  -Mode Complete
```

Como nenhum recurso é definido no modelo e o modo de implantação está definido para ser concluído, a rede virtual será excluída.

![Modo de implantação do modelo do Resource Manager e se o modo de implantação da saída de operação estiver completo](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

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

- Se você notar resultados incorretos da versão de visualização [https://aka.ms/whatifissues](https://aka.ms/whatifissues)do "e-se", por favor, informe os problemas em .
- Para implantar modelos com o Azure PowerShell, consulte [Implantar recursos com modelos ARM e PowerShell do Azure](deploy-powershell.md).
- Para implantar modelos com REST, consulte [Implantar recursos com modelos ARM e API REST do Gerenciador de recursos](deploy-rest.md).
